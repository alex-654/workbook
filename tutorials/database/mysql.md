### max_connections variable

So, what is the best value for max_connections? The answer is “it depends”. It will mainly depend upon two things, the
client load and the hardware MySQL is running on.

Based on accumulated experience we recommend at maximum 4 times number of user threads as the number of real CPU cores

### What Limits Thread Concurrency?

https://dev.mysql.com/blog-archive/mysql-connection-handling-and-scaling/
A thread will happily execute instructions until it needs to wait for something or until it has used its timeshare as
decided by the OS scheduler. There are three things a thread might need to wait for:  A mutex, a database lock, or IO.

    MySQL is very good at handling many clients connecting and disconnecting to the database at a high frequency, up to 80 thousand connect and disconnects per second
    MySQL scales well on multi-core CPUs and can deliver up to 2 million primary key look-ups per second on 48 CPU cores.
    Rule of thumb: Max number of connections = 4 times available CPU cores
    Efficient use of connections will depend upon user load, useful number of user connections can even be lower than number of CPU cores when the bottleneck is somewhere else than on the threading
    Check out your own load by doubling the number of connections until TPS no longer increases and latency starts to increase
