# ConvNet Drawer

Python script for illustrating Convolutional Neural Networks (CNN).
Inspired by the draw_convnet project [1].

Models can be visualized via Keras-like ([Sequential](https://keras.io/models/sequential/)) model definitions.
The result can be saved as SVG file or pptx file!

## Requirements
python-pptx

```sh
pip install python-pptx
```

## Usage
Write a script to define and save a model. An example of visualizing AlexNet [2] is as follows.

### Write and save convnet_drawer.Model

```python
from convnet_drawer import Model, Conv2D, MaxPooling2D, Flatten, Dense
from pptx_util import save_model_to_pptx

model = Model(input_shape=(227, 227, 3))
model.add(Conv2D(96, (11, 11), (4, 4)))
model.add(MaxPooling2D((3, 3), strides=(2, 2)))
model.add(Conv2D(256, (5, 5), padding="same"))
model.add(MaxPooling2D((3, 3), strides=(2, 2)))
model.add(Conv2D(384, (3, 3), padding="same"))
model.add(Conv2D(384, (3, 3), padding="same"))
model.add(Conv2D(256, (3, 3), padding="same"))
model.add(MaxPooling2D((3, 3), strides=(2, 2)))
model.add(Flatten())
model.add(Dense(4096))
model.add(Dense(4096))
model.add(Dense(1000))

# save as svg file
model.save_fig("example.svg")

# save as pptx file
save_model_to_pptx(model, "example.pptx")
```

Result:

<img src="examples/AlexNet.svg">

The other examples can be found [here](examples).

### Convert Keras sequential model
Keras sequential model can be converted to `convnet_drawer.Model` (thanks to @wakamezake).
Only Conv2D, MaxPooling2D, Flatten, Dense layers are supported for this conversion.

```python
from keras_util import convert_drawer_model
from keras_models import AlexNet
from pptx_util import save_model_to_pptx

# get Keras sequential model
keras_sequential_model = AlexNet.get_model()
model = convert_drawer_model(keras_sequential_model)

# save as svg file
model.save_fig("example.svg")

# save as pptx file
save_model_to_pptx(model, "example.pptx")
```

### Supported Layers

#### Conv2D
```Conv2D(filters=None, kernel_size=None, strides=(1, 1), padding="valid")```

e.g. `Conv2D(96, (11, 11), (4, 4)))`


#### MaxPooling2D, AveragePooling2D
```MaxPooling2D(pool_size=(2, 2), strides=None, padding="valid")```

e.g. `MaxPooling2D((3, 3), strides=(2, 2))`

If `strides = None`, stride is set to be `pool_size`.

#### GlobalAveragePooling2D
```GlobalAveragePooling2D()```

#### Flatten
```Flatten()```

#### Dense
```Dense(units)```

e.g. `Dense(4096)`

### Visualization Parameters
Visualization Parameters can be found in [config.py](config.py).
Please adjust these parameters using `configure` function before model definition (see [LeNet.py](examples/LeNet.py)).
The most important parameter is `channel_scale = 3 / 5`.
This parameter rescale actual channel size `c` to `c_` for visualization as:

```c_ = math.pow(c, channel_scale)```

If the maximum channel size is small (e.g. 512), please increase `channel_scale`.

Check how the other parameters works:

<img src="parameters.png">


## TODOs
- [x] Implement padding option for Conv2D and Pooling layers.
- [x] Add some effects to Dense layer (and Flatten / GlobalAveragePooling2D).
- [ ] Automatically calibrate the scale of feature maps for better visibility.
- [x] Move hard-coded parameters to a config file or options.
- [ ] Refactor Layer classes.
- [x] ~~Draw with matplotlib? for other formats.~~ The model is now directly saved as a pptx file.

## Results
LeNet

<img src="examples/LeNet.svg">

AlexNet

<img src="examples/AlexNet.svg">

ZFNet

<img src="examples/ZFNet.svg">

VGG16

<img src="examples/VGG16.svg">

## References
[1] https://github.com/gwding/draw_convnet

[2] A. Krizhevsky, I. Sutskever, and G. E. Hinton, "ImageNet Classification with Deep Convolutional Neural Networks," in Proc. of NIPS, 2012.