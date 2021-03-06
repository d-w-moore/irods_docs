Logging in 4.3+ has been changed significantly.  There is a new logging library that utilizes spdlog and syslog.

rodsLog() is called from several places within the iRODS code base.  Replacing all calls to it with the new library is going to take some time.  In order to help with this transition, the rodsLog implementation has been modified. It will continue to operate as it has in the past, but it will now forward all messages to the new library as well.

## Features

- Uses Syslog
- No files to manage or write to
- Output is in JSON format and is easily parseable
- Allows administrators to use common tools for analysis and management
- Cleaner API

## Usage

```c++
#include "irods_logger.hpp"
using log = irods::experimental::log;
log::<category>::<log_level>(args ...);
```

Where `<category>` can be one of the following:

- legacy
- server
- agent
- resource
- database
- auth
- api
- msi
- network
- rule_engine

A **category** is simply a tag that identifies a particular logger. All logger properties are keyed off of the category.

Where `<log_level>` is one of the following:

- trace
- debug
- info
- warn
- error
- critical

Where `args` is one of the following:

- std::string
- std::initializer_list <log::key_value\>
- Container <log::key_value\> (**Container** must support **begin/end** iterators)

## Creating a new Category

Three steps:

1. Declare a category tag.
2. Specialize the `logger_config` for the new category tag.
3. Use the logger with the category tag.
    
See the example below:

```c++
#include "irods_logger.hpp"

// 1. Declare the custom category tag.
//    This does not need to have a body.
//    This tag allows the logger to locate data specific to the new category.
struct my_category;

// 2. Specialize the logger configuration for the new category.
//    This also defines the default configuration for the new category.
namespace irods::experimental
{
    template <>
    class log::logger_config<my_category>
    {
        // This defines the name that will appear in the log under
        // the "src" key.  The "src" key defines where the message originated.
        // Try to use a name that makes it easy for administrators
        // to determine what produced the message.
        static constexpr char name[] = "my_category";

        // This is the current log level for the category.
        // This also represents the initial log level.
        // Use the set_level() function to adjust the level.
        static inline log::level level = log::level::info;

        // This is required since the fields above are private.
        // This allows the logger to access and modify the configuration.
        friend class logger<my_category>;
    };
} // namespace irods::experimental

// 3. Use the logger with the new category
void example()
{
    using log = irods::experimental::log;

    // Here is the logger with the new category.
    using logger = log::logger<my_category>; 

    // This message will be recorded.
    logger::info("Hello, My Category!!!");

    // Increase the log level for the category from "info" to "warn".
    // Now, only messages with a level greater than or equal to "warn" will
    // be recorded.  All other messages will be discarded.
    logger::set_level(log::level::warn);

    // This debug message will not be recorded because the log level
    // for the category is set to a higher level (warn > debug).
    logger::debug("This message will not be recorded.");
}
```
