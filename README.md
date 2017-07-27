# shufflenet in caffe
A naive caffe implementation of ShuffleNet , read the original paper for details:  
[ShuffleNet: An Extremely Efficient Convolutional Neural Network for Mobile Devices](https://arxiv.org/pdf/1707.01083.pdf) 
# How does shuffle work？
According to the paper, suppose a convolutional layer with g,groups whose output has g ×n channels;
we first reshape the output channel dimension into (g,n), transposing and then flattening it back as the input of next layer.
# Usage
- At first you should merge caffe folder to your own caffe
- Add these to your caffe.proto  

```
message LayerParameter {
  ......
  optional PermuteParameter permute_param = 202;
  ......
}

message PermuteParameter {
  // The new orders of the axes of data. Notice it should be with
  // in the same range as the input data, and it starts from 0.
  // Do not provide repeated order.
  repeated uint32 order = 1;
}

```

- shuffle unit example of group=4

```
layer {
  name: "conv2_1/reshape/1"
  type: "Reshape"
  bottom: "conv2_1/pointwise/1"
  top: "conv2_1/reshape/1"
  reshape_param {
    shape {
      dim: 50
      dim: 4
      dim: 17
    }
    num_axes: 2
  }
}
layer {
  name: "conv2_1/permute"
  type: "Permute"
  bottom: "conv2_1/reshape/1"
  top: "conv2_1/permute"
  permute_param {
    order: 0
    order: 2
    order: 1
    order: 3
    order: 4
  }
}
layer {
  name: "conv2_1/reshape/2"
  type: "Reshape"
  bottom: "conv2_1/permute"
  top: "conv2_1/reshape/2"
  reshape_param {
    shape {
      dim: 50
      dim: 68
    }
    num_axes: 3
  }
}
```
