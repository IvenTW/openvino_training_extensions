# Semantic segmentation sample
This sample demonstrates DL model compression capabilities for semantic segmentation problem
 
## Features:
- UNet and ICNet with implementations as close as possible to the original papers
- Loaders for CamVid, Cityscapes (20-class), Mapillary Vistas(20-class), Pascal VOC (reuses the loader integrated into torchvision)
- Configuration file examples for sparsity and quantization
- Export to ONNX compatible with OpenVINO 
- DataParallel and DistributedDataParallel modes
- Tensorboard output

## Quantize FP32 pre-trained model
This scenario demonstrates quantization with fine-tuning of UNet on Mapillary Vistas dataset.

#### Dataset preparation
- Obtain a copy of Mapillary Vistas train/val data [here](https://www.mapillary.com/dataset/vistas/)

#### Run semantic segmentation sample
- If you did not install the package then add the repository root folder to the `PYTHONPATH` environment variable
- Navigate to the `examples/segmentation` folder
- Run the following command to start compression with fine-tuning on GPUs:
`python main.py -m train --config configs/unet_mapillary_int8.json --data <path_to_dataset> --weights <path_to_fp32_model_checkpoint>`

It may take a few epochs to get the baseline accuracy results.
- Use `--multiprocessing-distributed` flag to run in the distributed mode.
- Use `--resume` flag with the path to a model from the previous experiment to resume training.
- Use `-b <number>` option to specify the total batch size across GPUs

#### Validate your model checkpoint
To estimate the test scores of your model checkpoint use the following command:
`python main.py -m test --config=configs/unet_mapillary_int8.json --resume <path_to_trained_model_checkpoint>`
If you want to validate an FP32 model checkpoint, make sure the compression algorithm settings are empty in the configuration file or `pretrained=True` is set.

#### Export compressed model
To export trained model to ONNX format use the following command:
`python main.py --mode test --config configs/unet_mapillary_int8.json --data <path_to_dataset> --resume <path_to_compressed_model_checkpoint> --to-onnx unet_int8.onnx`

#### Export to OpenVINO Intermediate Representation (IR)

To export a model to OpenVINO IR and run it using Intel Deep Learning Deployment Toolkit please refer to this [tutorial](https://software.intel.com/en-us/openvino-toolkit).
  
### Results
  
| Model | Compression Algorithms | Dataset | mIoU | Config path | PyTorch checkpoint |
| :-- | :-: | :-: | :-: | :-: | :-: |
| UNet | None (FP32) | CamVid | 71.95 | examples/segmentation/configs/unet_camvid.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/unet_camvid.pth) |
| UNet | Int8 | CamVid | 71.82 | examples/segmentation/configs/unet_camvid_int8.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/unet_camvid_int8.pth) |
| UNet | Int8 + 60% magnitude sparsity  | CamVid | 71.90 | examples/segmentation/configs/unet_camvid_magnitude_sparsity_int8.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/unet_camvid_magnitude_sparsity_int8.pth) |
| UNet | None (FP32) | Mapillary | 56.23 | examples/segmentation/configs/unet_mapillary.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/unet_mapillary.pth) |
| UNet | Int8 | Mapillary | 56.16 | examples/segmentation/configs/unet_mapillary_int8.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/unet_mapillary_int8.pth) |
| UNet | Int8 + 60% magnitude sparsity | Mapillary | 54.30 | examples/segmentation/configs/unet_mapillary_magnitude_sparsity_int8.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/unet_mapillary_magnitude_sparsity_int8.pth) |
| ICNet | None (FP32) | CamVid | 67.89 | examples/segmentation/configs/icnet_camvid.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/icnet_camvid.pth) |
| ICNet | Int8 | CamVid | 67.69 | examples/segmentation/configs/icnet_camvid_int8.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/icnet_camvid_int8.pth) |
| ICNet | Int8 + 60% magnitude sparsity | CamVid | 67.53 | examples/segmentation/configs/icnet_camvid_magnitude_sparsity_int8.json | [Link](https://download.01.org/opencv/openvino_training_extensions/models/nncf/icnet_camvid_magnitude_sparsity_int8.pth) |
