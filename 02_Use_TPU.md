# How to use TPU?
## Readings
I highly recommend you to read the pages below
1. [PyTorch XLA](https://github.com/pytorch/xla)
2. [XLA performance guide](https://github.com/pytorch/xla/blob/master/TROUBLESHOOTING.md)
3. [Huggingface Accelerate](https://huggingface.co/docs/accelerate/)

## Install packages
```
pip3 install --upgrade transformers accelerate datasets
```

## Run experiments w/ 🤗 Accelerate!
### What is 🤗 Accelerate?
+ 🤗 Accelerate provides an easy API to make your scripts run with mixed precision and on any kind of distributed setting (multi-GPUs, TPUs etc.) while still letting you write your own training loop. The same code can then runs seamlessly on your local machine for debugging or your training environment.

+ 🤗 Accelerate also provides a CLI tool that allows you to quickly configure and test your training environment then launch the scripts.

### How to use Accelerate?
#### Modify the PyTorch code.
0. Prepare your Vanilla PyTorch codes. 
1. Import the Accelerator main class instantiate one in an accelerator object:
```
from accelerate import Accelerator

accelerator = Accelerator()
```
This should happen as early as possible in your training script!

2. Remove the call to(device) or cuda() for your model and input data.

3. Pass all objects relevant to training (optimizer, model, training dataloader) to the prepare() method. This will make sure everything is ready for training.
```
model, optimizer, train_dataloader = accelerator.prepare(model, optimizer, train_dataloader)
```
4. Replace the line loss.backward() by accelerator.backward(loss)
```
accelerator.backward(loss) <-- loss.backward()
```
#### Run experiments!
In the shell,
```
accelerate config
accelerate launch [file_name].py <-- was python [file_name].py
```
Done 😊. Enjoy!

### Example codes
https://github.com/huggingface/accelerate/tree/main/examples
