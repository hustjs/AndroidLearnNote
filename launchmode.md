优秀文章：
https://developer.android.com/guide/components/tasks-and-back-stack.html
http://blog.csdn.net/guolin_blog/article/details/41087993
http://blog.csdn.net/zhangjg_blog/article/details/10923643
http://blog.csdn.net/luoshengyang/article/details/6714543
四种启动模式：


2种定义方式:1 使用manifest文件  2 使用intent flag

#manifest方式：设置launchMode属性：

"standard"（默认模式）
    默认。系统在启动 Activity 的任务中创建 Activity 的新实例并向其传送 Intent。Activity 可以多次实例化，而每个实例均可属于不同的任务，并且一个任务可以拥有多个实例。 
"singleTop"
    如果当前任务的顶部已存在 Activity 的一个实例，则系统会通过调用该实例的 onNewIntent() 方法向其传送 Intent，而不是创建 Activity 的新实例。Activity 可以多次实例化，而每个实例均可属于不同的任务，并且一个任务可以拥有多个实例（但前提是位于返回栈顶部的 Activity 并不是 Activity 的现有实例）。

    例如，假设任务的返回栈包含根 Activity A 以及 Activity B、C 和位于顶部的 D（堆栈是 A-B-C-D；D 位于顶部）。收到针对 D 类 Activity 的 Intent。如果 D 具有默认的 "standard" 启动模式，则会启动该类的新实例，且堆栈会变成 A-B-C-D-D。但是，如果 D 的启动模式是 "singleTop"，则 D 的现有实例会通过 onNewIntent() 接收 Intent，因为它位于堆栈的顶部；而堆栈仍为 A-B-C-D。但是，如果收到针对 A 类 Activity 的 Intent，则会向堆栈添加 B 的新实例，即便其启动模式为 "singleTop" 也是如此。

    注：为某个 Activity 创建新实例时，用户可以按“返回”按钮返回到前一个 Activity。 但是，当 Activity 的现有实例处理新 Intent 时，则在新 Intent 到达 onNewIntent() 之前，用户无法按“返回”按钮返回到 Activity 的状态。
"singleTask"
    系统创建新任务并实例化位于新任务底部的 Activity。但是，如果该 Activity 的一个实例已存在于一个单独的任务中，则系统会通过调用现有实例的 onNewIntent() 方法向其传送 Intent，而不是创建新实例。一次只能存在 Activity 的一个实例。

    注：尽管 Activity 在新任务中启动，但是用户按“返回”按钮仍会返回到前一个 Activity。 
"singleInstance"。
    与 "singleTask" 相同，只是系统不会将任何其他 Activity 启动到包含实例的任务中。该 Activity 始终是其任务唯一仅有的成员；由此 Activity 启动的任何 Activity 均在单独的任务中打开。


#使用 Intent 标志

启动 Activity 时，您可以通过在传递给 startActivity() 的 Intent 中加入相应的标志，修改 Activity 与其任务的默认关联方式。可用于修改默认行为的标志包括：

FLAG_ACTIVITY_NEW_TASK
    在新任务中启动 Activity。如果已为正在启动的 Activity 运行任务，则该任务会转到前台并恢复其最后状态，同时 Activity 会在 onNewIntent() 中收到新 Intent。

    正如前文所述，这会产生与 "singleTask" launchMode 值相同的行为。
FLAG_ACTIVITY_SINGLE_TOP
    如果正在启动的 Activity 是当前 Activity（位于返回栈的顶部），则 现有实例会接收对 onNewIntent() 的调用，而不是创建 Activity 的新实例。

    正如前文所述，这会产生与 "singleTop" launchMode 值相同的行为。
FLAG_ACTIVITY_CLEAR_TOP
    如果正在启动的 Activity 已在当前任务中运行，则会销毁当前任务顶部的所有 Activity，并通过 onNewIntent() 将此 Intent 传递给 Activity 已恢复的实例（现在位于顶部），而不是启动该 Activity 的新实例。

    产生这种行为的 launchMode 属性没有值。

    FLAG_ACTIVITY_CLEAR_TOP 通常与 FLAG_ACTIVITY_NEW_TASK 结合使用。一起使用时，通过这些标志，可以找到其他任务中的现有 Activity，并将其放入可从中响应 Intent 的位置。

    注：如果指定 Activity 的启动模式为 "standard"，则该 Activity 也会从堆栈中删除，并在其位置启动一个新实例，以便处理传入的 Intent。 这是因为当启动模式为 "standard" 时，将始终为新 Intent 创建新实例。




manifest其他属性：
关联属性taskAffinity：
    在两种情况下，关联会起作用：

    启动 Activity 的 Intent 包含 FLAG_ACTIVITY_NEW_TASK 标志。

    默认情况下，新 Activity 会启动到调用 startActivity() 的 Activity 任务中。它将推入与调用方相同的返回栈。 但是，如果传递给 startActivity() 的 Intent 包含 FLAG_ACTIVITY_NEW_TASK 标志，则系统会寻找其他任务来储存新 Activity。这通常是新任务，但未做强制要求。 如果现有任务与新 Activity 具有相同关联，则会将 Activity 启动到该任务中。 否则，将开始新任务。

    如果此标志导致 Activity 开始新任务，且用户按“主页”按钮离开，则必须为用户提供导航回任务的方式。 有些实体（如通知管理器）始终在外部任务中启动 Activity，而从不作为其自身的一部分启动 Activity，因此它们始终将 FLAG_ACTIVITY_NEW_TASK 放入传递给 startActivity() 的 Intent 中。请注意，如果 Activity 能够由可以使用此标志的外部实体调用，则用户可以通过独立方式返回到启动的任务，例如，使用启动器图标（任务的根 Activity 具有 CATEGORY_LAUNCHER Intent 过滤器；请参阅下面的启动任务部分）。
    Activity 将其 allowTaskReparenting 属性设置为 "true"。

    在这种情况下，Activity 可以从其启动的任务移动到与其具有关联的任务（如果该任务出现在前台）。

    例如，假设将报告所选城市天气状况的 Activity 定义为旅行应用的一部分。 它与同一应用中的其他 Activity 具有相同的关联（默认应用关联），并允许利用此属性重定父级。当您的一个 Activity 启动天气预报 Activity 时，它最初所属的任务与您的 Activity 相同。 但是，当旅行应用的任务出现在前台时，系统会将天气预报 Activity 重新分配给该任务并显示在其中。

    提示：如果从用户的角度来看，一个 .apk 文件包含多个“应用”，则您可能需要使用 taskAffinity 属性将不同关联分配给与每个“应用”相关的 Activity。

清空返回栈属性：

alwaysRetainTaskState
    如果在任务的根 Activity 中将此属性设置为 "true"，则不会发生刚才所述的默认行为。即使在很长一段时间后，任务仍将所有 Activity 保留在其堆栈中。 
clearTaskOnLaunch
    如果在任务的根 Activity 中将此属性设置为 "true"，则每当用户离开任务然后返回时，系统都会将堆栈清除到只剩下根 Activity。 换而言之，它与 alwaysRetainTaskState 正好相反。 即使只离开任务片刻时间，用户也始终会返回到任务的初始状态。 
finishOnTaskLaunch
    此属性类似于 clearTaskOnLaunch，但它对单个 Activity 起作用，而非整个任务。 此外，它还有可能会导致任何 Activity 停止，包括根 Activity。 设置为 "true" 时，Activity 仍是任务的一部分，但是仅限于当前会话。如果用户离开然后返回任务，则任务将不复存在。 

下面深入介绍singleTask和singleInstance
参考链接：http://blog.csdn.net/zhangjg_blog/article/details/10923643
singleTask: 
启动launchMode设为singleTask的activity的过程:
1发现启动模式为singleTask，设启动标志为FLAG_ACTIVITY_NEW_TASK；
2获得（检测）该activity的taskaffinity；
3检测是否存在affinity的task；
4若存在这样的task则检索是否存在该activity的实例，若存在将该activity置于栈顶（清空任务栈内上方所以activity），不存在则在栈顶创建这个Activity；
5若不存在这样的task，在创建此affinity的新task，并在新task内启动该activity

重要特性归纳：
#任务（Task）不仅可以跨应用（Application），还可以跨进程（Process）
#在同一个任务中具有唯一性
#启动一个singleTask的Activity实例时，如果系统中已经存在这样一个实例，就会将这个实例调度到任务栈的栈顶，并清除它当前所在任务中位于它上面的所有的activity。

singleInstance:


# 1 以singleInstance模式启动的Activity具有全局唯一性，即整个系统中只会存在一个这样的实例
# 2 以singleInstance模式启动的Activity具有独占性，即它会独自占用一个任务，被他开启的任何activity都会运行在其他任务中（官方文档上的描述为，singleInstance模式的Activity不允许其他Activity和它共存在一个任务中）
# 3 被singleInstance模式的Activity开启的其他activity，能够开启一个新任务，但不一定开启新的任务，也可能在已有的一个任务中开启


生命周期相关：
以MainActivity 启动SecondActivity为例，生命周期过程为：M onCreate->M onStart ->onResume->在MainActivity中启动SecondActivity->M onPause->S onCreate -> 
S onStart->S onResume ->M onStop -按下back键-> S onPause ->S onStop->S onDestroy
-> (M onRestart-> M onStart-> )M onResume-> 再次按下back键-> M onPause -> M onStop ->M onDestroy
