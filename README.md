<img title="" src="./icon.png" alt="icon.png" data-align="center" width="413">

## Extensible network framework for federated learning system

FL-Network is a simple Network architecture for federated learning. It is suitable for parameter server mode and P2P mode of federated learning.FL-Network uses Redis as multi-terminal interaction messaging middleware, which supports the construction and simulation of federated learning networks in standalone and distributed environments. Researchers can use FL-Network to quickly build an extensible federated learning system. Through The API of FL-Network, multi-terminal message transmission, such as weight(FedAvg), gradient(FedSGD) etc., can be performed without considering the underlying Network. In this way, researchers can focus more on the implementation of federated learning algorithm on FL-Network. At the same time, there is no need to install complex federated learning framework, which is beneficial for researchers to learn the specific implementation process of federated learning algorithm.

### Installation for FL-Network

You can install Fl-Network with pip or install manually.

#### With pip

```shell
~$ pip install fl-network
```

#### Manually

Download the source distribution or built distribution in release.

```shell
~$ tar -zxvf ./fl-network-0.0.1.tar.gz
~$ cd fl-network-0.0.1
~$ python setup.py install
```

or use built distribution.

```shell
~$ pip install fl_network-0.0.1-py3-none-any.whl # wheel
~$ pip install fl_network-0.0.1-py3.8.egg # egg
```

### Installation for Redis

Fl-network uses Redis as multi-terminal interaction messaging middleware. You can install redis on [WSL2(Windows Subsystem for Linux)](https://redis.io/docs/getting-started/installation/install-redis-on-windows/), [Linux](https://redis.io/docs/getting-started/installation/install-redis-on-linux/) and [MacOS](https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/). If you prefer to install redis on Windows, please refer to [here](https://github.com/MicrosoftArchive/redis/releases).

### Quick start with demo

Before getting start to program your FL algorithm, you must launch redis server on isolated server or localhost and export ip and port of redis. (default 127.0.0.1:6379)

```python
from FLNetwork.paramServer import ParamServer
from FLNetwork.device import Device

if __name__ == '__main__':

    # setup()
    # setup FL device: dev_01 locally (ip=127.0.0.1, port=6379) 
    dev_01 = Device(name="dev_01",
                    server="param_sys", peers=["dev_02"],
                    args_from_ser=["weight", "grad"], args_from_peer=[],
                    ip="127.0.0.1", port=6379)

    # setup FL device: dev_02 locally (ip=127.0.0.1, port=6379) 
    dev_02 = Device(name="dev_02",
                    server="param_sys", peers=["dev_01"],
                    args_from_ser=["weight", "grad"], args_from_peer=[],
                    ip="127.0.0.1", port=6379)

    # setup FL parameters server: param_sys locally (ip=127.0.0.1, port=6379) 
    server = ParamServer(name="param_sys", 
                         devices=["dev_01", "dev_02"], args_from_dev=["weight", "grad"],
                         ip="127.0.0.1", port=6379)
```

**The setup parameters Illustration**

| Class       | Parameter          | Illustration                                            |
|:-----------:|:--------------:|:-------------------------------------------------------:|
| Device      | name           | The device name is used as the identifier               |
| -           | server         | The server name is used as the identifier               |
| -           | peers          | The list of other peers                                 |
| -           | args_from_ser  | The  predefined args that will be received from servere |
| -           | args_from_peer | The  predefined args that will be received from peer    |
| -           | ip             | Ip of redis                                             |
| -           | port           | Port of redis                                           |
| ParamServer | name           | The server name is used as the identifier               |
| -           | devices        | The list of devices                                     |
| -           | args_from_dev  | The  predefined args that will be received from devices |
| -           | ip             | Ip of redis                                             |
| -           | port           | Port of redis                                           |

### API

#### Parameters Server

---

Define a new arg from device:

```python
server.set_from_arg(arg, device) # arg: arg name (str), device: target device name (str)
```

Send a arg to device by predefined arg channel above:

```python
server.send(arg, value) # arg: arg name (str), value: value of arg (Any)
```

Read arg from device:

```python
server.read(dev, arg) # dev: device name (str), arg: name of arg to read (str)
```

Get arg channel (to be received):

```python
channel = server.get_channel() # return channel dict
```

Set global arg (anyone can read):

```python
server.set(global_arg, value) # global_arg: global arg name (str), value: value of arg (Any)
```

Get global arg:

```python
arg = server.get(global_arg) # global_arg: global arg name (str)
```

#### Device

---

Define a new arg from peer or server:

```python
dev_01.set_from_arg(arg, dev_ser, is_server=False) # arg: arg name (str), dev_ser: target device/server name (str)
```

Send a arg to device/server by predefined arg channel above:

```python
dev_01.send(arg, value) # arg: arg name (str), value: value of arg (Any)
```

Read arg from device:

```python
dev_01.read_dev(dev, arg) # dev: device name (str), arg: name of arg to read (str)
```

Read arg from server:

```python
dev_01.read_ser(ser, arg) # dev: server name (str), arg: name of arg to read (str)
```

Get arg channel (to be received):

```python
channel = dev_01.get_channel() # return channel dict
```

Set global arg (anyone can read):

```python
dev_01.set(global_arg, value) # global_arg: global arg name (str), value: value of arg (Any)
```

Get global arg:

```python
arg = dev_01.get(global_arg) # global_arg: global arg name (str)
```

### Working Example

```python
# 1. dev_01 define a new arg from server
dev_01.set_from_arg("init_weight", server.name)
# 2. parameters server initialize model
import torch
weight = torch.zeros((1000, 1000))
server.send("init_weight", weight)
# 3. device receive model weight
init_weight = dev_01.rend_ser(server.name, "init_weight")
# 4. set init_weight as net.parameters() in Pytorch
```

### Contact

Project Investigator: Rostar (luosida@qq.com)



