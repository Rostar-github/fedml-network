<img title="" src="file:///F:/repos/fedml-network/icon.png" alt="icon.png" data-align="center" width="413">

### Extensible network framework for federated learning system



```python
from fl_network.device import Device
from fl_network.paramServer import ParamServer

if __name__ == '__main__':
    # setup()
    dev_01 = Device(name="dev_01",
                    server="param_sys", peers=["dev_02"],
                    args_from_ser=["weight", "grad"], args_from_peer=[])

    dev_02 = Device(name="dev_02",
                    server="param_sys", peers=["dev_01"],
                    args_from_ser=["weight", "grad"], args_from_peer=[])

    server = ParamServer(name="param_sys", devices=["dev_01", "dev_02"], args_from_dev=["weight", "grad"])
```