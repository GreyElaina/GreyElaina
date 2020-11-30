项目地址: https://github.com/GraiaProject/BroadcastControl

**虽然这个项目是我为一个 QQ 机器人框架实现的事件系统, 但即使是单独使用也是没有任何问题的**

## 动机

在 pypi 上找不到一个符合我要求的事件系统(`blinker` 不支持 asyncio), 并且自己也想借鉴下 `FastAPI` 的参数获取方式, 就自己手搓了一个.

## 目前的情况

暂时没有给这个项目做单独文档的精力, 但其稳定性已经在上面那个框架中得到体现.

我设计了一个 Dispatcher 的设计用于规范声明的参数的解析, 单个/多个的 Dispatcher 都可以作为参数解析器使用(~~突发奇想的一个名称~~):

```python
class Dispatcher(BaseDispatcher):
    def catch(self, interface: DispatcherInterface):
        if interface.name == "graia" and interface.annotation is str:
            return "i'm here."
```

以上所声明的 Dispatcher, 可以解析这样的 Callable 声明:

```python
def test_execute_target(graia: str):
    print(graia) # 输出: "i'm here"
```

多个 Dispatcher 可以下落式的被调用, 从而可以使 Dispatcher 各司其职.  
Dispatcher 还设计了 mixin 模式, 可以引入其他 Dispatcher 的功能.

此外, 事件是这样声明的:

```python
class TestEvent(BaseEvent):
    test_field: str
    
    class Dispatcher(BaseDispatcher):
        def catch(self, interface):
            if interface.name == "test": return interface.event.test_field
```

BaseEvent 的子类都是 pydantic 的 BaseModel, 不允许乱来.

这个事件系统使用 asyncio 的基本 API 实现, 侵入性尽可能的达到了最低. 此外, 还有很多特性, 例如 `Decorator`(参数装饰器), `Interrupt`(中断) 等没有在这里提到, 有兴趣的人可以在仓库的 `src/test.py` 处看看, 我也使用 `Decorator` 实现了类似 `FastAPI` 的 `Depend` 和 `Middleware`, 但更强大.

此外, 你可以在这里阅读我为 Graia Application 写的文档中的对 Broadcast Control 的介绍和基本特性介绍: https://graia-document.vercel.app/docs/broadcast-control/bcc-about

希望能得到意见和支持, 在此感谢大家.
