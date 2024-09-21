---
title: "简单的日志系统的搭建"
date: 2022-08-06 23:51:06 +0800
tags: ["cpp"]
---

在写一些简单的玩具的时候，我们可以通过标准库提供的 `printf` 或者 `std::cout` 等进行调试。不过当项目越来越大的时候，就需要一个日志系统帮助我们监测程序详细的运行状态及调试，所以有必要实现一个好用的日志系统。虽然现在已经有很多现成的日志库可以直接拿来用了，但是自己实现一个也不是很难。本篇文章参考 [sylar](https://github.com/sylar-yin/sylar) 的日志系统实现了一个简化的日志系统

## 日志系统的基本功能

1. 日志等级
2. 自定义输出信息
3. 日志发生文件的位置
4. 日志发生的时间
5. 自定义日志输出位置

除此之外，还可以给出日志发生的线程号，程序运行时间等信息，这里不再考虑

## 日志系统的实现

### 日志分级

日志分为如下 6 种级别

- `TRACE`：程序运行过程的详细信息，一般不使用
- `DEBUG`：调试时使用，构建 Release 版本时不会产生日志信息
- `INFO`：提供给用户的普通信息
- `WARN`：程序没有报错但出现危险的操作
- `ERROR`：发生错误但程序仍可正常运行
- `FATAL`：造成程序无法正常执行

下面给出日志分级的类 `LogLevel` 的声明与实现

```cpp
class LogLevel {
 public:
  enum Level {
    TRACE = 0,
    DEBUG,
    INFO,
    WARN,
    ERROR,
    FATAL,
  };

  /**
   * @brief 将日志级别转为字符串
   *
   * @param level
   * @param color 输出到控制台时设为 true 可显示彩色的日志信息
   * @return std::string
   */
  static std::string ToString(Level level, bool color = false);
};
```

`ToString()` 方法的通过 switch 关键字即可实现，显示彩色字体用到了 [ANSI escape code](https://en.wikipedia.org/wiki/ANSI_escape_code)，下面是详细的实现过程

```cpp
std::string LogLevel::ToString(Level level, bool color) {
  std::string str = "";
  if (color) {
    switch (level) {
#define xx(LEVEL, COLOR) \
  case LEVEL:            \
    str += COLOR;        \
    break
      xx(TRACE, "\033[93m");
      xx(DEBUG, "\033[32m");
      xx(INFO, "\033[34m");
      xx(WARN, "\033[91m");
      xx(ERROR, "\033[31m");
      xx(FATAL, "\033[35m");
#undef xx
    }
  }
  switch (level) {
#define xx(LEVEL)  \
  case LEVEL:      \
    str += #LEVEL; \
    break
    xx(TRACE);
    xx(DEBUG);
    xx(INFO);
    xx(WARN);
    xx(ERROR);
    xx(FATAL);
#undef xx
  }
  if (color) str += "\033[0m";
  return str;
}
```

### 日志发生的状态封装

日志产生时，除了日志内的 message，还有该位置日志发生的状态信息，包括：

1. 日志发生文件
2. 日志发生行号
3. 日志出现时间

这些信息都封装到 `LogEvent` 中，与 message 段分开处理

```cpp
struct LogEvent {
  LogEvent(int line_, const char* filename_, uint64_t time_)
      : line(line_), filename(filename_), time(time_) {}
  int line;              // 日志发生的行数
  const char* filename;  // 日志发生的文件
  uint64_t time;         // 日志出现的时间
};
```

### 日志的产生器

日志产生器用来产生并输入日志信息：通过 `log()` 函数产生日志内容，再借助 `LogAppender` 将日志输出到合适的位置

```cpp
class Logger {
 public:
  Logger(const std::string& name, std::shared_ptr<LogAppender> appender);

  void log(LogLevel::Level level, struct LogEvent event, const char* fmt, ...);

 private:
  std::string name_;  // 日志名称
  std::shared_ptr<LogAppender> appender_;
};
```

下面是 `log()` 的实现过程。在这一步，sylar 实现了通过配置文件自定义日志输出格式的功能，为了简化，这里就直接写死了，个人使用问题不大

```cpp
void Logger::log(LogLevel::Level level, struct LogEvent event, const char* fmt,
                 ...) {
  // 获取 message 信息到 msgBuf
  va_list al;
  va_start(al, fmt);
  char* msgBuf = nullptr;
  int len = vasprintf(&msgBuf, fmt, al);
  va_end(al);

  std::stringstream str;
  // 处理时间
  struct tm tm;
  time_t time = event.time;
  localtime_r(&time, &tm);
  std::string timeFormat = "%Y-%m-%d %H:%M:%S";
  char timeBuf[64];
  strftime(timeBuf, sizeof(timeBuf), timeFormat.c_str(), &tm);
  // 整合日志内容到 str
  str << LogLevel::ToString(level, true) << "\t" << timeBuf << "\t"
      << event.filename << ":" << event.line << "\t" << msgBuf;
  free(msgBuf);

  // 输出
  appender_->output(str.str());
}
```

### 日志的输出位置封装

日志可以有多个不同的输出位置，比如：

- 控制台
- 文件
- 邮箱
- ...

这里我们实现控制台和文件的输出封装。首先提供一个抽象类 `LogAppender` 给 `Logger` 使用，然后 `StdoutLogAppender` 和 `FileLogAppender` 分别继承该类

```cpp
class LogAppender {
 public:
  virtual void output(const std::string& str) = 0;
};

class StdoutLogAppender : public LogAppender {
 public:
  void output(const std::string& str) override {
    std::cout << str << std::endl;
  }
};

class FileLogAppender : public LogAppender {
 public:
  FileLogAppender(const std::string& filename): filename_(filename) {}
  void output(const std::string& str) override {
    std::ofstream f;
    f.open(filename_, ios::app);
    f << str << endl;
    f.close();
  }
 private:
  std::string filename_;
};
```

`StdoutLogAppender` 使用 `std::cout` 将日志信息直接打印到控制台即可。`FileLogAppender` 的 `output()` 实现比较简单粗暴，每次写日志时重新打开一次文件。其实这里可以有其他设计，比如添加一个缓冲区定时往文件中写入，不同级日志输出到不同文件等

### 日志管理器的实现

每个日志产生器 `Logger` 肯定是全局唯一的，我们要使用[单例模式](https://zhuanlan.zhihu.com/p/37469260)。这里为了方便拓展，使用 `LogManager` 对日志产生器进行初始化。当然，有哪些类型的 `Logger` 同样在这里写死了，其他的日志输出方式我暂时并不需要

```cpp
class LogManager {
 public:
  static Logger* Get(const std::string& name = "default") {
    static std::map<std::string, Logger*> s_loggers;
    auto it = s_loggers.find(name);
    if (it == s_loggers.end()) {
      if (name == "default") {
        s_loggers[name] =
            new Logger("default", std::make_shared<StdoutLogAppender>());
      } else {
        // TODO
      }
    }
    return s_loggers[name];
  }
};
```

### 辅助宏的实现

先看代码吧：

```cpp
// 对 LogEvent 内容进行封装
#define LOG_FMT(level, fmt, args...) \
  LogManager::Get()->log(   \
      level, LogEvent(__LINE__, __FILENAME__, time(0)), fmt, ##args)

#if DEBUG
#define LOG_DEBUG(fmt, args...) \
  LOG_FMT(LogLevel::Level::DEBUG, fmt, ##args)
#else
#define LOG_DEBUG(fmt, args...)
#endif

#define LOG_INFO(fmt, args...) \
  LOG_FMT(LogLevel::Level::INFO, fmt, ##args)
#define LOG_TRACE(fmt, args...) \
  LOG_FMT(LogLevel::Level::TRACE, fmt, ##args)
#define LOG_WARN(fmt, args...) \
  LOG_FMT(LogLevel::Level::WARN, fmt, ##args)
#define LOG_ERROR(fmt, args...) \
  LOG_FMT(LogLevel::Level::ERROR, fmt, ##args)
#define LOG_FATAL(fmt, args...) \
  LOG_FMT(LogLevel::Level::FATAL, fmt, ##args)

```

这里的主要难点是对可变参数的处理，使用 `args...` 和 `##args` 是一种比较灵活的方法：

```cpp
LOG_INFO("Hello %s", "World") // '...' 和    'args...' 均通过
LOG_INFO("Hello World")       // '...' 会报错 'args...' 正常
```

然后还有一点，上面的宏 `__FILENAME__` 是通过 `cmake` 产生的，这是为了保证在输出文件路径时只含有本项目内的目录信息：

```bash
/home/miaohn/code/log/test/test.log:6 # 使用 __FILE__
test/test.log:6                       # 使用 __FILENAME__
```

产生该宏的 cmake 函数如下：

```cmake
function(define_filename_macro targetname)
  get_target_property(source_files "${targetname}" SOURCES)
  foreach(sourcefile ${source_files})
    # Get source file's current list of compile definitions.
    get_property(defs SOURCE "${sourcefile}"
        PROPERTY COMPILE_DEFINITIONS)
    # Get the relative path of the source file in project directory
    get_filename_component(filepath "${sourcefile}" ABSOLUTE)
    string(REPLACE ${PROJECT_SOURCE_DIR}/ "" relpath ${filepath})
    list(APPEND defs "__FILENAME__=\"${relpath}\"")
    # Set the updated compile definitions on the source file.
    set_property(
      SOURCE "${sourcefile}"
      PROPERTY COMPILE_DEFINITIONS ${defs}
      )
  endforeach()
endfunction()
```

定义完上述函数后，在 `add_executable(name source)` 后，紧跟 `define_filename_macro(name)` 即可:

```cmake
add_executable(log_test log_test.cpp)
define_filename_macro(log_test)
```

## 效果展示

测试文件为：

```cpp
#include "log/log.h"

int main(int argc, char const *argv[]) {
  LOG_TRACE("Hello %s", "minicraft");
  LOG_TRACE("Hello minicraft");
  LOG_DEBUG("Hello minicraft");
  LOG_INFO("Hello minicraft");
  LOG_WARN("Hello minicraft");
  LOG_ERROR("Hello minicraft");
  LOG_FATAL("Hello minicraft");
  return 0;
}
```

日志输出的效果如下图：

![log](https://cdn.jsdelivr.net/gh/MiaoHN/image-host@master/images/20240921092743.png)
