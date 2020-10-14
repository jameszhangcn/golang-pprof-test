# Guide for using golang pprof

## 1 In code, add th import.

   ``` import _ "net/http/pprof" ```

## 2 start the HTTP server

	go func() {
	    log.Println(http.ListenAndServe("localhost:6067", nil))
	}()

## 3 if using k8s docker, need forward the port to host.

    kubectl port-forward srmsvc-97c464847-d9gvq -n mvnr-mtcil1-appln-cuup-cuup1 6067:6067

## 4 on the host, shall install golang, check the CPU usage during 30 seconds

    ./go tool pprof http://localhost:6067/debug/pprof/profile?seconds=30

## 5 Demo
	
Normal:

	(pprof) top
	Showing nodes accounting for 60ms, 100% of 60ms total
	Showing top 10 nodes out of 34
	      flat  flat%   sum%        cum   cum%
	      20ms 33.33% 33.33%       20ms 33.33%  syscall.Syscall
	      10ms 16.67% 50.00%       10ms 16.67%  gcommon/impl/services/common/haaas/heartbeat/flatbuffers/HbInterface.HeartbeatEnd
	      10ms 16.67% 66.67%       10ms 16.67%  runtime.futex
	      10ms 16.67% 83.33%       20ms 33.33%  runtime.makeslice
	      10ms 16.67%   100%       10ms 16.67%  runtime.memclrNoHeapPointers
	         0     0%   100%       20ms 33.33%  bufio.(*Writer).Flush
	         0     0%   100%       50ms 83.33%  gcommon/impl/services/common/haaas/heartbeat.SendHeartbeatToCIM
	         0     0%   100%       50ms 83.33%  gcommon/impl/services/common/haaas/statenotify.startTimer
	         0     0%   100%       20ms 33.33%  github.com/google/flatbuffers/go.NewBuilder (inline)
	         0     0%   100%       20ms 33.33%  github.com/nats-io/nats%2ego.(*Conn).Flush (inline)
	(pprof) exit
	[root@k8s-master-0 bin]# 
	[root@k8s-master-0 bin]# 

Abnormal:

	[root@k8s-master-0 bin]# ./go tool pprof http://localhost:6067/debug/pprof/profile?seconds=30
	Fetching profile over HTTP from http://localhost:6067/debug/pprof/profile?seconds=30
	Saved profile in /root/pprof/pprof.srmsvc.samples.cpu.002.pb.gz
	File: srmsvc
	Type: cpu
	Time: Oct 14, 2020 at 2:44am (UTC)
	Duration: 30.19s, Total samples = 7.40s (24.51%)
	Entering interactive mode (type "help" for commands, "o" for options)
	(pprof) top
	Showing nodes accounting for 7.34s, 99.19% of 7.40s total
	Dropped 10 nodes (cum <= 0.04s)
	      flat  flat%   sum%        cum   cum%
	     3.69s 49.86% 49.86%      3.69s 49.86%  runtime.chanrecv
	     2.19s 29.59% 79.46%      5.89s 79.59%  runtime.selectnbrecv
	     1.36s 18.38% 97.84%      7.28s 98.38%  up_sp/impl/services/srmsvc/srmserver.waitSwitchOver
	     0.09s  1.22% 99.05%      0.09s  1.22%  runtime.usleep
	     0.01s  0.14% 99.19%      0.11s  1.49%  runtime.sysmon
	         0     0% 99.19%      7.28s 98.38%  main.main
	         0     0% 99.19%      7.28s 98.38%  runtime.main
	         0     0% 99.19%      0.11s  1.49%  runtime.mstart
	         0     0% 99.19%      0.11s  1.49%  runtime.mstart1
	         0     0% 99.19%      7.28s 98.38%  up_sp/impl/services/srmsvc/srmserver.WaitRoleAssign
	(pprof) 