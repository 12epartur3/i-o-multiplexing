select和poll都需要自己维护一个数组来保存fd，内核将就绪的数组拷贝到用户态，每次在用户态都会遍历数组来获取i/o事件，调用时再将数组拷贝到核心态，核心态也会遍历数组进行事件注册。
poll相比select，fd参数数组不是值-结果参数，每次可以复用上次的数组，只需要将不关心的fd设置为-1即可，select每次调用都需要重新设置所有fd，另外select只支持1024个fd。
epoll需要把关心的fd通过epoll_ctl传给epfd,或者删掉，每次epoll_wait只会返回就绪的i/o时间，避免了大量无用的拷贝和遍历。
epoll的lt（水平触发）和et（边缘触发），et表示epoll只会讲就绪事件返回一次，用户必须全部处理，lt表示若用户未处理就绪事件，epoll还会在下次进行返回。
