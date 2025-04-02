任务控制块信息中添加任务调用次数
```rs
/// The task control block (TCB) of a task.
#[derive(Copy, Clone)]
pub struct TaskControlBlock {
    /// The task status in it's lifecycle
    pub task_status: TaskStatus,
    /// The task context
    pub task_cx: TaskContext,
    /// call count
    pub syscall_count: [u32; crate::config::MAX_SYSCALL_NUM]
}
```

每个系统调用被调用前增加任务调用次数
```rs
/// handle syscall exception with `syscall_id` and other arguments
pub fn syscall(syscall_id: usize, args: [usize; 3]) -> isize {
    match syscall_id {
        SYSCALL_WRITE => {
            TASK_MANAGER.syscall_count_inc(syscall_id);
            sys_write(args[0], args[1] as *const u8, args[2])
        },
        SYSCALL_EXIT => {
            TASK_MANAGER.syscall_count_inc(syscall_id);
            sys_exit(args[0] as i32)
        },
        SYSCALL_YIELD => {
            TASK_MANAGER.syscall_count_inc(syscall_id);
            sys_yield()
        },
        SYSCALL_GET_TIME => {
            TASK_MANAGER.syscall_count_inc(syscall_id);
            sys_get_time(args[0] as *mut TimeVal, args[1])
        },
        SYSCALL_TRACE => {
            TASK_MANAGER.syscall_count_inc(syscall_id);
            sys_trace(args[0], args[1], args[2])
        },
        _ => panic!("Unsupported syscall_id: {}", syscall_id),
    }
}
```