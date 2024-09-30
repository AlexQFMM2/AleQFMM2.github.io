---
title: Redis池与Redis管理
categories: 阅读
date: 2024-09-30 10:20:45
tags: 网络



---

# Redis池与Redis管理

<!--more-->

## 什么是Redis

Redis 是一个**开源的内存数据库**，非常快速，主要用于**缓存**和**高效的数据存储**。它的特别之处在于所有数据都保存在**内存**中，所以读写速度非常快，适合处理大量的快速请求。它支持很多数据结构，比如：

1. **字符串**：可以用来存储简单的键值对。
2. **列表**：可以存储有序的一系列字符串，类似于数组。
3. **集合**：存储独特的无序元素，适合去重操作。
4. **有序集合**：带有权重的集合，可以按顺序获取数据。
5. **哈希表**：可以存储对象的字段和值，非常适合像用户信息这样的数据。

Redis 通常用于以下场景：

- **缓存**：比如网站的用户会话信息，用来减少数据库的访问次数，加快响应速度。
- **排行榜**：比如游戏里的排名系统，因为有序集合可以按分数快速排序。
- **消息队列**：Redis 支持发布/订阅功能，能够用来做消息通知。

由于 Redis 是内存型数据库，**数据存储在内存中**，这虽然提高了速度，但也意味着如果没有做持久化（保存到磁盘），断电后数据可能会丢失。不过 Redis 也支持数据**快照**和**日志**来持久化数据，确保数据安全。



## 具体代码实现

### 1、**RedisPool.h**pp

```
#ifndef REDISPOOL_H  
#define REDISPOOL_H  

#include <hiredis/hiredis.h>  //"/usr/include/hiredis"
#include <queue>  
#include <mutex>  
#include <string>  
#include <iostream>  
#include <stdexcept>  
#include <memory>  
#include <condition_variable>  
#include <functional>

class RedisPool {  
public:  
    RedisPool(const std::string& host, int port, int pool_size);  
    ~RedisPool();  

    void releaseConnection(redisContext* conn);  
    std::unique_ptr<redisContext, std::function<void(redisContext*)>> getConnection();  
    

private:  
    std::queue<redisContext*> connectionPool; // 连接池  
    std::mutex mtx; // 互斥锁  
    int maxPoolSize; // 最大连接数  
    std::condition_variable cv; // 条件变量  

    std::string host;  
    int port;  
};  

#endif // REDISPOOL_H
```

#### 解读1、redisContext

`redisContext` 是 Redis 的 C 语言客户端库 `hiredis` 中的一个结构体，主要用于管理客户端和 Redis 服务器之间的**连接**。它包含连接状态、服务器地址、端口号等信息，负责维护与 Redis 服务器的通信。



### 2、RedisPool.cpp

```
#include "RedisPool.hpp"  

RedisPool::RedisPool(const std::string& host, int port, int pool_size)  
    : host(host), port(port), maxPoolSize(pool_size) {  
    for (int i = 0; i < maxPoolSize; ++i) {  
        redisContext* ctx = redisConnect(host.c_str(), port);  
        if (ctx == nullptr || ctx->err) {  
            if (ctx) {  
                std::cerr << "Error connecting to Redis: " << ctx->errstr << std::endl;  
                redisFree(ctx);  
            } else {  
                std::cerr << "Can't allocate Redis context" << std::endl;  
            }  
        } else {  
            connectionPool.push(ctx); // 将连接加入池中  
        }  
    }  
}  

RedisPool::~RedisPool() {  
    while (!connectionPool.empty()) {  
        redisFree(connectionPool.front()); // 释放连接  
        connectionPool.pop();  
    }  
}  

// 使用 unique_ptr 进行连接的获取  
std::unique_ptr<redisContext, std::function<void(redisContext*)>> RedisPool::getConnection() {  
    std::unique_lock<std::mutex> lock(mtx); // 加锁  
    cv.wait(lock, [this] { return !connectionPool.empty(); }); // 等待直到连接可用  

    redisContext* conn = connectionPool.front();  
    connectionPool.pop();  
    
    //std::cout << "pool can use thread : " << connectionPool.size() << std::endl;

    return std::unique_ptr<redisContext, std::function<void(redisContext*)>>(
        conn, 
        [this](redisContext* conn) { this->releaseConnection(conn); } // 使用 lambda 捕获 this 指针
    );
}  

void RedisPool::releaseConnection(redisContext* conn) {  
    if (conn) {  
        {  
            std::lock_guard<std::mutex> lock(mtx);  
            connectionPool.push(conn); // 将连接放回池中  
        }  
        //std::cout << "pool can use thread : " << connectionPool.size() << std::endl; 
        cv.notify_one(); // 通知等待的线程  
    }  
}
```

```
大致和数据库池差不多，毕竟本身也是内存数据库
```

### 3、RedisManager.hpp

```
#ifndef REDISMANAGER_H  
#define REDISMANAGER_H  

#include <string>  
#include <vector>  
#include <hiredis/hiredis.h>  
#include "RedisPool.hpp"  

class RedisManager {  
public:  
    RedisManager(const std::string& host, int port);  
    ~RedisManager();  

    void saveUserSession(const std::string& username, int client_sock);  
    void updateUserActivity(const std::string& username);  
    void checkUserTimeout();  
    int getUserSocket(const std::string& username);  
    std::vector<std::string> getAllActiveUsers();
    void print_online_user(std::vector<std::string>& activeUsers);
private:  
    RedisPool redis_pool; // Redis 连接池 
    long long wait_time; // 超时等待时间   
};  

#endif // REDISMANAGER_H
```



### 4、RedisManager.cpp

```
#include "RedisManager.hpp"  

std::mutex user_mutex; // 用于保护用户数据结构的互斥锁

RedisManager::RedisManager(const std::string& host, int port)  
    : redis_pool(host, port, 10), wait_time(25) { // 初始化连接池，设置超时等待时间  
}  

RedisManager::~RedisManager() {  
    // 连接池会在其析构函数中自动处理  
    std::cout << "RedisManager destroyed." << std::endl;  
}  

void RedisManager::saveUserSession(const std::string& username, int client_sock) {  
    auto conn = redis_pool.getConnection(); // 从连接池获取连接 

    // 获取当前时间并转换为秒  
    auto now_seconds = std::chrono::system_clock::now().time_since_epoch();  
    auto now = std::chrono::duration_cast<std::chrono::seconds>(now_seconds).count(); // 转换为秒  

    //std::cout << "[" << std::to_string(now).c_str() << "] : " << username << " is Login by socket : " << client_sock << std::endl;   
    
    // 保存用户 socket (以字符串形式存储)  
    redisCommand(conn.get(), "SET %s %d", username.c_str(), client_sock);  

    // 保存最后活动时间（以秒为字符串形式存储）  
    redisCommand(conn.get(), "SET %s:last_active %s", username.c_str(), std::to_string(now).c_str());   

    // 设置最后活动时间的过期时间  
    redisCommand(conn.get(), "EXPIRE %s:last_active %lld", username.c_str(), wait_time);  

     // 将用户添加到活跃用户集合中  
    redisCommand(conn.get(), "SADD active_users %s", username.c_str());  

}

void RedisManager::updateUserActivity(const std::string& username) {  
    auto conn = redis_pool.getConnection(); // 从连接池获取连接    

    // 获取当前时间并转换为秒  
    auto now_seconds = std::chrono::system_clock::now().time_since_epoch();  
    auto now = std::chrono::duration_cast<std::chrono::seconds>(now_seconds).count(); // 转换为秒  

    // 更新用户最后活动时间（以秒为字符串形式存储）  
    redisCommand(conn.get(), "SET %s:last_active %s", username.c_str(), std::to_string(now).c_str());  

    // 可选：如果您希望在每次活动时更新过期时间，可以设置过期时间  
    redisCommand(conn.get(), "EXPIRE %s:last_active %lld", username.c_str(), wait_time); 

}

std::vector<std::string> RedisManager::getAllActiveUsers() {  
    auto conn = redis_pool.getConnection(); // 从连接池获取连接 

    std::vector<std::string> activeUsers; // 用于存储活跃用户列表  

    // 获取集合中的所有成员  
    redisReply* reply = (redisReply*)redisCommand(conn.get(), "SMEMBERS active_users");  
    
    if (reply && reply->type == REDIS_REPLY_ARRAY) {  
        for (size_t i = 0; i < reply->elements; ++i) {  
            activeUsers.emplace_back(reply->element[i]->str); // 将用户名添加到列表中  
        }  
    }  
    
    freeReplyObject(reply); // 释放 Redis 回复对象  

    return activeUsers; // 返回活跃用户列表  
}

void RedisManager::print_online_user(std::vector<std::string>& activeUsers)
{
    std::cout <<" now online user : " << std::endl;
    for(auto s:activeUsers){
        std::cout << s << " ";
    }
    std::cout << std::endl;
}

void RedisManager::checkUserTimeout() {  
    auto conn = redis_pool.getConnection(); // 从连接池获取连接  

    if(conn == nullptr){
        std::cout << "connect error" << std::endl;
        return;
    }

    // 获取当前时间并转换为秒  
    auto now_seconds = std::chrono::system_clock::now().time_since_epoch();  
    auto current_time = std::chrono::duration_cast<std::chrono::seconds>(now_seconds).count(); // 转换为秒  
    std::cout << "current_time is : " << current_time << std::endl;
    // 假设有一个获取所有用户会话的命令  
    std::vector<std::string> users = getAllActiveUsers(); // 需要实现此方法以获取所有活跃用户  

    //print_online_user(users);

    for (const auto& username : users) {  
        // 获取最后活动时间  
        std::cout << "---------------------------------" << std::endl;
        std::cout << username << ": " << std::endl;
        redisReply* reply = (redisReply*)redisCommand(conn.get(), "GET %s:last_active", username.c_str());  
        
        if (!reply) {  
            std::cerr << "Failed to execute command for user " << username << std::endl;  
            continue; // 跳过当前用户，继续下一个  
        }  

        if (reply->type == REDIS_REPLY_NIL) {  
            std::cout << "User " << username << " is no longer active (data not found)." << std::endl;  
            
            redisCommand(conn.get(), "DEL %s", username.c_str());  
            redisCommand(conn.get(), "DEL %s:sock", username.c_str());  
            redisCommand(conn.get(), "DEL %s:last_active", username.c_str());  
            
            freeReplyObject(reply); // 释放 reply 对象  
            continue; // 如果用户数据不在，跳过  
        }  

        if (reply && reply->type == REDIS_REPLY_STRING) {  
            long long last_active_time = std::stoll(reply->str); // 将字符串转换为 long long  
            std::cout << " last_active_time is : " << last_active_time << std::endl;
            std::cout << " no activity time is : " << current_time - last_active_time  << std::endl;
        }  

        freeReplyObject(reply); // 释放 Redis 回复对象  
        std::cout << "---------------------------------" << std::endl;
    }  
}

int RedisManager::getUserSocket(const std::string& username) {  
    auto conn = redis_pool.getConnection(); // 从连接池获取连接  

    redisReply* reply = (redisReply*)redisCommand(conn.get(), "GET %s", username.c_str());  

    int socket = -1;  
    if (reply) {  
        if (reply->type == REDIS_REPLY_STRING) {  
            socket = std::stoi(reply->str); // 将字符串转换为整数  
        } else {  
            std::cout << "No socket found for user: " << username << std::endl;  
        }  
        freeReplyObject(reply); // 释放 Redis 回复对象  
    } else {  
        std::cerr << "Failed to execute command: " << conn->errstr << std::endl;  
    }  
    return socket; // 返回 socket 值  
}
```

#### 解读1、std::chrono::system_clock::now().time_since_epoch()

std::chrono::system_clock::now().time_since_epoch() 是一个 C++ 中用于获取自纪元（epoch）以来经过的时间的常用操作。它与 std::chrono 库结合使用，提供了一种高精度、跨平台的方式来测量时间和时间点。

###### 使用方式

```
#include <iostream>
#include <chrono>

int main() {
    // 获取当前时间点
    auto current_time = std::chrono::system_clock::now();
    
    // 获取自纪元以来的时间差
    auto duration = current_time.time_since_epoch();
    
    // 将时间差转换为秒
    auto seconds = std::chrono::duration_cast<std::chrono::seconds>(duration);
    std::cout << "Seconds since epoch: " << seconds.count() << "s\n";

    // 将时间差转换为毫秒
    auto milliseconds = std::chrono::duration_cast<std::chrono::milliseconds>(duration);
    std::cout << "Milliseconds since epoch: " << milliseconds.count() << "ms\n";

    return 0;
}
```

#### 解读2、redisCommand

`redisCommand` 是 Redis 的 C 语言客户端库 `hiredis` 中用于向 Redis 服务器发送命令并获取结果的核心函数。通过 `redisCommand`，你可以在 C 程序中发送任何 Redis 支持的命令，并接收返回的结果。

**构建命令**：根据传入的格式化字符串，生成 Redis 命令。

**发送命令**：将命令通过客户端与 Redis 服务器的连接发送出去。

**接收结果**：等待 Redis 服务器执行命令后返回结果，并将结果封装在 `redisReply` 结构体中。

redis官方文档

https://redis.com.cn/documentation.html

#### 解读3、redisReply

结构体一览

```
typedef struct redisReply {
    int type;        // 响应的类型
    long long integer; // 如果是整数类型，存储整数值
    size_t len;     // 字符串的长度
    char *str;      // 字符串值
    size_t elements; // 数组（如列表、集合等）的元素数量
    struct redisReply **element; // 指向子元素的指针
} redisReply;

```

其中type表示响应的类型，可能的值有：
REDIS_REPLY_STRING：字符串类型。
REDIS_REPLY_INTEGER：整数类型。
REDIS_REPLY_ARRAY：数组类型（如列表、集合）。
REDIS_REPLY_NIL：表示空值（通常对应 Redis 的 nil）。
REDIS_REPLY_STATUS：状态信息（如命令成功或失败的消息）。
REDIS_REPLY_ERROR：错误信息（如命令执行失败时的错误消息）。
通过 reply->type 可以判断响应的类型，进而处理数据。

使用后用

```
freeReplyObject(reply);
```

释放