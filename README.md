# webfux-issue around error handler

ref. https://jira.spring.io/browse/SPR-16231
ref. https://github.com/spring-projects/spring-boot/issues/11168

At first, run the spring-boot app.

    ./mvnw spring-boot:run

Then, access too many times using wrk or other performance testing tool.

    $ wrk http://localhost:8080
    Running 10s test @ http://localhost:8080
      2 threads and 10 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     4.86ms    9.28ms 135.18ms   92.36%
        Req/Sec     2.00k     1.02k    4.30k    59.00%
      39751 requests in 10.02s, 3.87MB read
    Requests/sec:   3968.69
    Transfer/sec:    395.34KB

Then, you'll get the stacktrace like this:

```
2017-11-29 09:24:12.451 ERROR 15017 --- [ctor-http-nio-4] .a.w.r.e.DefaultErrorWebExceptionHandler : Failed to handle request [GET http://localhost:8080/]

java.io.IOException: Broken pipe
	at sun.nio.ch.FileDispatcherImpl.writev0(Native Method) ~[na:1.8.0_151]
	at sun.nio.ch.SocketDispatcher.writev(SocketDispatcher.java:51) ~[na:1.8.0_151]
	at sun.nio.ch.IOUtil.write(IOUtil.java:148) ~[na:1.8.0_151]
	at sun.nio.ch.SocketChannelImpl.write(SocketChannelImpl.java:504) ~[na:1.8.0_151]
	at io.netty.channel.socket.nio.NioSocketChannel.doWrite(NioSocketChannel.java:403) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannel$AbstractUnsafe.flush0(AbstractChannel.java:934) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.nio.AbstractNioChannel$AbstractNioUnsafe.flush0(AbstractNioChannel.java:362) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannel$AbstractUnsafe.flush(AbstractChannel.java:901) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.DefaultChannelPipeline$HeadContext.flush(DefaultChannelPipeline.java:1321) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeFlush0(AbstractChannelHandlerContext.java:776) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeFlush(AbstractChannelHandlerContext.java:768) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.flush(AbstractChannelHandlerContext.java:749) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.CombinedChannelDuplexHandler$DelegatingChannelHandlerContext.flush(CombinedChannelDuplexHandler.java:533) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.ChannelOutboundHandlerAdapter.flush(ChannelOutboundHandlerAdapter.java:115) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.CombinedChannelDuplexHandler.flush(CombinedChannelDuplexHandler.java:358) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeFlush0(AbstractChannelHandlerContext.java:776) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeFlush(AbstractChannelHandlerContext.java:768) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.flush(AbstractChannelHandlerContext.java:749) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.ChannelDuplexHandler.flush(ChannelDuplexHandler.java:117) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeFlush0(AbstractChannelHandlerContext.java:776) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.invokeFlush(AbstractChannelHandlerContext.java:768) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.AbstractChannelHandlerContext.flush(AbstractChannelHandlerContext.java:749) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at reactor.ipc.netty.channel.ChannelOperationsHandler$PublisherSender.onComplete(ChannelOperationsHandler.java:505) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at reactor.core.publisher.FluxMap$MapSubscriber.onComplete(FluxMap.java:130) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at org.springframework.http.server.reactive.ChannelSendOperator$WriteBarrier.onComplete(ChannelSendOperator.java:200) ~[spring-web-5.0.1.RELEASE.jar:5.0.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable$MapFuseableSubscriber.onComplete(FluxMapFuseable.java:138) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MonoSubscriber.complete(Operators.java:1094) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoSupplier.subscribe(MonoSupplier.java:61) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxSourceMonoFuseable.subscribe(FluxSourceMonoFuseable.java:38) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable.subscribe(FluxMapFuseable.java:63) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at org.springframework.http.server.reactive.ChannelSendOperator.subscribe(ChannelSendOperator.java:76) ~[spring-web-5.0.1.RELEASE.jar:5.0.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapMain.onNext(MonoFlatMap.java:150) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MonoSubscriber.complete(Operators.java:1092) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapInner.onNext(MonoFlatMap.java:241) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxOnErrorResume$ResumeSubscriber.onNext(FluxOnErrorResume.java:73) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxPeekFuseable$PeekFuseableSubscriber.onNext(FluxPeekFuseable.java:198) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxPeekFuseable$PeekFuseableSubscriber.onNext(FluxPeekFuseable.java:198) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MonoSubscriber.complete(Operators.java:1092) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenAcceptInner.onNext(MonoIgnoreThen.java:290) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$ScalarSubscription.request(Operators.java:1649) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenAcceptInner.onSubscribe(MonoIgnoreThen.java:279) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxFlatMap.trySubscribeScalarMap(FluxFlatMap.java:161) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:53) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoDefer.subscribe(MonoDefer.java:52) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenIgnoreMain.drain(MonoIgnoreThen.java:148) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen.subscribe(MonoIgnoreThen.java:56) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoPeekFuseable.subscribe(MonoPeekFuseable.java:74) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoPeekFuseable.subscribe(MonoPeekFuseable.java:74) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapMain.onNext(MonoFlatMap.java:150) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxSwitchIfEmpty$SwitchIfEmptySubscriber.onNext(FluxSwitchIfEmpty.java:67) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoNext$NextSubscriber.onNext(MonoNext.java:76) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap$ConcatMapImmediate.innerNext(FluxConcatMap.java:271) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap$ConcatMapInner.onNext(FluxConcatMap.java:798) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable$MapFuseableSubscriber.onNext(FluxMapFuseable.java:115) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$ScalarSubscription.request(Operators.java:1649) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable$MapFuseableSubscriber.request(FluxMapFuseable.java:156) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MultiSubscriptionSubscriber.set(Operators.java:1463) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MultiSubscriptionSubscriber.onSubscribe(Operators.java:1337) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable$MapFuseableSubscriber.onSubscribe(FluxMapFuseable.java:90) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoJust.subscribe(MonoJust.java:54) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoMapFuseable.subscribe(MonoMapFuseable.java:59) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Mono.subscribe(Mono.java:2913) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap$ConcatMapImmediate.drain(FluxConcatMap.java:418) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap$ConcatMapImmediate.onSubscribe(FluxConcatMap.java:210) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxIterable.subscribe(FluxIterable.java:91) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxIterable.subscribe(FluxIterable.java:55) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap.subscribe(FluxConcatMap.java:121) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoNext.subscribe(MonoNext.java:40) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoSwitchIfEmpty.subscribe(MonoSwitchIfEmpty.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:60) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:60) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Mono.subscribe(Mono.java:2913) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenIgnoreMain.drain(MonoIgnoreThen.java:167) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen.subscribe(MonoIgnoreThen.java:56) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoPeekTerminal.subscribe(MonoPeekTerminal.java:61) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.ipc.netty.channel.ChannelOperations.applyHandler(ChannelOperations.java:388) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at reactor.ipc.netty.http.server.HttpServerOperations.onHandlerStart(HttpServerOperations.java:359) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at io.netty.util.concurrent.AbstractEventExecutor.safeExecute(AbstractEventExecutor.java:163) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.SingleThreadEventExecutor.runAllTasks(SingleThreadEventExecutor.java:403) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.nio.NioEventLoop.run(NioEventLoop.java:463) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.SingleThreadEventExecutor$5.run(SingleThreadEventExecutor.java:858) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at java.lang.Thread.run(Thread.java:748) ~[na:1.8.0_151]

2017-11-29 09:24:12.471 ERROR 15017 --- [ctor-http-nio-4] o.s.w.s.adapter.HttpWebHandlerAdapter    : Failed to handle request

java.lang.UnsupportedOperationException: null
	at java.util.Collections$UnmodifiableMap.put(Collections.java:1457) ~[na:1.8.0_151]
	at org.springframework.http.HttpHeaders.set(HttpHeaders.java:1439) ~[spring-web-5.0.1.RELEASE.jar:5.0.1.RELEASE]
	at org.springframework.http.HttpHeaders.setContentType(HttpHeaders.java:849) ~[spring-web-5.0.1.RELEASE.jar:5.0.1.RELEASE]
	at org.springframework.boot.autoconfigure.web.reactive.error.AbstractErrorWebExceptionHandler.write(AbstractErrorWebExceptionHandler.java:235) ~[spring-boot-autoconfigure-2.0.0.M6.jar:2.0.0.M6]
	at org.springframework.boot.autoconfigure.web.reactive.error.AbstractErrorWebExceptionHandler.lambda$handle$1(AbstractErrorWebExceptionHandler.java:228) ~[spring-boot-autoconfigure-2.0.0.M6.jar:2.0.0.M6]
	at reactor.core.publisher.MonoFlatMap$FlatMapMain.onNext(MonoFlatMap.java:118) [reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MonoSubscriber.complete(Operators.java:1092) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapInner.onNext(MonoFlatMap.java:241) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxPeekFuseable$PeekFuseableSubscriber.onNext(FluxPeekFuseable.java:198) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$ScalarSubscription.request(Operators.java:1649) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxPeekFuseable$PeekFuseableSubscriber.request(FluxPeekFuseable.java:138) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapInner.onSubscribe(MonoFlatMap.java:230) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxPeekFuseable$PeekFuseableSubscriber.onSubscribe(FluxPeekFuseable.java:172) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoJust.subscribe(MonoJust.java:54) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoPeekFuseable.subscribe(MonoPeekFuseable.java:74) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapMain.onNext(MonoFlatMap.java:150) [reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxSwitchIfEmpty$SwitchIfEmptySubscriber.onNext(FluxSwitchIfEmpty.java:67) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxSwitchIfEmpty$SwitchIfEmptySubscriber.onNext(FluxSwitchIfEmpty.java:67) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$ScalarSubscription.request(Operators.java:1649) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MultiSubscriptionSubscriber.set(Operators.java:1463) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MultiSubscriptionSubscriber.onSubscribe(Operators.java:1337) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoJust.subscribe(MonoJust.java:54) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoDefer.subscribe(MonoDefer.java:52) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Mono.subscribe(Mono.java:2913) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxSwitchIfEmpty$SwitchIfEmptySubscriber.onComplete(FluxSwitchIfEmpty.java:75) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators.complete(Operators.java:125) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoEmpty.subscribe(MonoEmpty.java:45) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoSwitchIfEmpty.subscribe(MonoSwitchIfEmpty.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoSwitchIfEmpty.subscribe(MonoSwitchIfEmpty.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:60) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:60) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Mono.subscribe(Mono.java:2913) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxOnErrorResume$ResumeSubscriber.onError(FluxOnErrorResume.java:97) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapMain.secondError(MonoFlatMap.java:185) [reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapInner.onError(MonoFlatMap.java:251) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxOnErrorResume$ResumeSubscriber.onError(FluxOnErrorResume.java:100) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators.error(Operators.java:175) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxFlatMap.trySubscribeScalarMap(FluxFlatMap.java:129) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:53) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Mono.subscribe(Mono.java:2913) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxOnErrorResume$ResumeSubscriber.onError(FluxOnErrorResume.java:97) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at org.springframework.http.server.reactive.ChannelSendOperator$WriteCompletionBarrier.onError(ChannelSendOperator.java:339) ~[spring-web-5.0.1.RELEASE.jar:5.0.1.RELEASE]
	at reactor.core.publisher.MonoNext$NextSubscriber.onError(MonoNext.java:87) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MultiSubscriptionSubscriber.onError(Operators.java:1332) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MonoSubscriber.onError(Operators.java:1135) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenAcceptInner.onError(MonoIgnoreThen.java:300) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.ipc.netty.FutureMono$FutureSubscription.operationComplete(FutureMono.java:159) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at io.netty.util.concurrent.DefaultPromise.notifyListener0(DefaultPromise.java:507) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.DefaultPromise.notifyListenersNow(DefaultPromise.java:481) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.DefaultPromise.notifyListeners(DefaultPromise.java:420) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.DefaultPromise.setFailure(DefaultPromise.java:113) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.DefaultChannelPromise.setFailure(DefaultChannelPromise.java:87) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at reactor.ipc.netty.channel.ChannelOperationsHandler$PublisherSender.operationComplete(ChannelOperationsHandler.java:612) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at reactor.ipc.netty.channel.ChannelOperationsHandler$PublisherSender.operationComplete(ChannelOperationsHandler.java:451) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at io.netty.util.concurrent.DefaultPromise.notifyListener0(DefaultPromise.java:507) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.DefaultPromise.notifyListenersNow(DefaultPromise.java:481) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.DefaultPromise.notifyListeners(DefaultPromise.java:420) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.DefaultPromise.addListener(DefaultPromise.java:163) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.DefaultChannelPromise.addListener(DefaultChannelPromise.java:93) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.DefaultChannelPromise.addListener(DefaultChannelPromise.java:28) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at reactor.ipc.netty.channel.ChannelOperationsHandler$PublisherSender.onComplete(ChannelOperationsHandler.java:521) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at reactor.core.publisher.FluxMap$MapSubscriber.onComplete(FluxMap.java:130) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at org.springframework.http.server.reactive.ChannelSendOperator$WriteBarrier.onComplete(ChannelSendOperator.java:200) ~[spring-web-5.0.1.RELEASE.jar:5.0.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable$MapFuseableSubscriber.onComplete(FluxMapFuseable.java:138) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MonoSubscriber.complete(Operators.java:1094) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoSupplier.subscribe(MonoSupplier.java:61) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxSourceMonoFuseable.subscribe(FluxSourceMonoFuseable.java:38) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable.subscribe(FluxMapFuseable.java:63) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at org.springframework.http.server.reactive.ChannelSendOperator.subscribe(ChannelSendOperator.java:76) ~[spring-web-5.0.1.RELEASE.jar:5.0.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapMain.onNext(MonoFlatMap.java:150) [reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MonoSubscriber.complete(Operators.java:1092) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapInner.onNext(MonoFlatMap.java:241) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxOnErrorResume$ResumeSubscriber.onNext(FluxOnErrorResume.java:73) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxPeekFuseable$PeekFuseableSubscriber.onNext(FluxPeekFuseable.java:198) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxPeekFuseable$PeekFuseableSubscriber.onNext(FluxPeekFuseable.java:198) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MonoSubscriber.complete(Operators.java:1092) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenAcceptInner.onNext(MonoIgnoreThen.java:290) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$ScalarSubscription.request(Operators.java:1649) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenAcceptInner.onSubscribe(MonoIgnoreThen.java:279) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxFlatMap.trySubscribeScalarMap(FluxFlatMap.java:161) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:53) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoDefer.subscribe(MonoDefer.java:52) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenIgnoreMain.drain(MonoIgnoreThen.java:148) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen.subscribe(MonoIgnoreThen.java:56) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoPeekFuseable.subscribe(MonoPeekFuseable.java:74) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoPeekFuseable.subscribe(MonoPeekFuseable.java:74) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap$FlatMapMain.onNext(MonoFlatMap.java:150) [reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxSwitchIfEmpty$SwitchIfEmptySubscriber.onNext(FluxSwitchIfEmpty.java:67) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoNext$NextSubscriber.onNext(MonoNext.java:76) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap$ConcatMapImmediate.innerNext(FluxConcatMap.java:271) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap$ConcatMapInner.onNext(FluxConcatMap.java:798) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable$MapFuseableSubscriber.onNext(FluxMapFuseable.java:115) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$ScalarSubscription.request(Operators.java:1649) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable$MapFuseableSubscriber.request(FluxMapFuseable.java:156) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MultiSubscriptionSubscriber.set(Operators.java:1463) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Operators$MultiSubscriptionSubscriber.onSubscribe(Operators.java:1337) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxMapFuseable$MapFuseableSubscriber.onSubscribe(FluxMapFuseable.java:90) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoJust.subscribe(MonoJust.java:54) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoMapFuseable.subscribe(MonoMapFuseable.java:59) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Mono.subscribe(Mono.java:2913) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap$ConcatMapImmediate.drain(FluxConcatMap.java:418) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap$ConcatMapImmediate.onSubscribe(FluxConcatMap.java:210) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxIterable.subscribe(FluxIterable.java:91) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxIterable.subscribe(FluxIterable.java:55) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.FluxConcatMap.subscribe(FluxConcatMap.java:121) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoNext.subscribe(MonoNext.java:40) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoSwitchIfEmpty.subscribe(MonoSwitchIfEmpty.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:60) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoFlatMap.subscribe(MonoFlatMap.java:60) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.Mono.subscribe(Mono.java:2913) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen$ThenIgnoreMain.drain(MonoIgnoreThen.java:167) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoIgnoreThen.subscribe(MonoIgnoreThen.java:56) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoOnErrorResume.subscribe(MonoOnErrorResume.java:44) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.core.publisher.MonoPeekTerminal.subscribe(MonoPeekTerminal.java:61) ~[reactor-core-3.1.1.RELEASE.jar:3.1.1.RELEASE]
	at reactor.ipc.netty.channel.ChannelOperations.applyHandler(ChannelOperations.java:388) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at reactor.ipc.netty.http.server.HttpServerOperations.onHandlerStart(HttpServerOperations.java:359) ~[reactor-netty-0.7.1.RELEASE.jar:0.7.1.RELEASE]
	at io.netty.util.concurrent.AbstractEventExecutor.safeExecute(AbstractEventExecutor.java:163) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.SingleThreadEventExecutor.runAllTasks(SingleThreadEventExecutor.java:403) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.channel.nio.NioEventLoop.run(NioEventLoop.java:463) ~[netty-transport-4.1.16.Final.jar:4.1.16.Final]
	at io.netty.util.concurrent.SingleThreadEventExecutor$5.run(SingleThreadEventExecutor.java:858) ~[netty-common-4.1.16.Final.jar:4.1.16.Final]
	at java.lang.Thread.run(Thread.java:748) ~[na:1.8.0_151]

```
