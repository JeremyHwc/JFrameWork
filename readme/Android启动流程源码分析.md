# Android启动流程源码分析
    手机 --通电--> bootloader分区（硬件初始化，接收按键输入） 
    
    会有以下三种启动路径：
    （1）--组合按键--> bootloader模式 <--fastboot--分区刷写
    （2）--组合按键--> recovery分区 -->Linux内核 -Init-recovery启动 <--系统更新固件、擦除用户数据
    （3）--正常模式--> boot分区 --> Linux内核
                   --> Init程序（Init进程：第一个用户进程，读取init.rc并执行，根据fstab分区表挂载分区，启动Zygote等/system/bin/app_process...）
                   --> App_main.cpp.main()(Zygote进程，Android系统的母进程，主要完成：解析app_process参数、设置当前进程名为zygote、调用AndroidRuntime.star)
                   --> AndroidRuntime.cpp.start()(启动虚拟机 startVm、注册JNI方法startReg、反射调用ZygoteInit.main)
                   --> ZygoteInit.java.main()(启动预加载preload()、startSystemServer会fork并配置子进程system_server、通过RuntimeInit反射调用启动SystemServer.main)
                   --> SystemServer.java.main()(system_server进程：系统服务的工作进程。（1）初始化Looper:Looper.prepareMainLooper
                                                                                       （2）加载服务的JNI库：System.loadLibrary
                                                                                       （3）创建SystemServiceManager
                                                                                       （4）启动Android服务
                                                                                            startBootstapServices
                                                                                            startCoreServices
                                                                                            startOtherServices(会执行ActivityManagerService))
                   
    
