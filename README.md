##Higher-Order-Implicit-Fairing-Networks-for-3D-Human-Pose-Estimation 
Higher-Order-Implicit-Fairing-Networks-for-3D-Human-Pose-Estimation.
### Results on Human3.6M

Ground truth detection rtesluts under Protocol 1 (mean per-joint position error) and Protocol 2 (mean per-joint position error after rigid alignment).

| Method | 2D Detections | # of Epochs | # of Parameters | MPJPE (P1) | P-MPJPE (P2) |
|:-------|:-------:|:-------:|:-------:|:-------:|:-------:|
| Martinez et al. [1] | Ground truth | 200  | 4.29M | 44.40 mm | 35.25 mm |
| SemGCN | Ground truth | 50 | 0.27M | 42.14 mm | 33.53 mm |
| SemGCN (w/ Non-local) | Ground truth | 30 | 0.43M | 40.78 mm | 31.46 mm |
| HGCN   | Ground truth | 50 |  1.20M  | 39.52 mm | 31.07 mm |
| HOIF-Net(Ours)   | Ground truth | 50 |  1.20M  | **39.16 mm** | **30.83 mm** |


CPN detection rtesluts under Protocol 1 (mean per-joint position error) and Protocol 2 (mean per-joint position error after rigid alignment).

| Method | 2D Detections | # of Epochs | # of Parameters | MPJPE (P1) | P-MPJPE (P2) |
|:-------|:-------:|:-------:|:-------:|:-------:|:-------:|
| HGCN   | CPN deteciton | 50 |  1.20M  | 55.60 mm | 43.70 mm |
| HOIF-Net(Ours)   | CPN deteciton | 50 |  1.20M  | **54.80 mm** | **42.90 mm** |

The results are borrowed from [SemGCN](https://github.com/garyzhao/SemGCN) and [High-order GCN](https://github.com/ZhimingZo/HGCN).




## Quickstart

This repository is build upon Python v3.7 and Pytorch v1.3.1 on Anaconda. All experiments are conducted on a single NVIDIA RTX 2070 Super GPU. See [`requirements.txt`](requirements.txt) for other dependencies. We recommend installing Python v3.7 from [Anaconda](https://www.anaconda.com/) and installing Pytorch (>= 1.3.1) following guide on the [official instructions](https://pytorch.org/) according to your specific CUDA version. Then you can install dependencies with the following commands.

```
git clone https://github.com/happyvictor008/Higher-Order-Implicit-Fairing-Networks-for-3D-Human-Pose-Estimation.git
cd Higher-Order-Implicit-Fairing-Networks-for-3D-Human-Pose-Estimation
pip install -r requirements.txt
```

### Dataset setup
CPN 2D detections for Human3.6M datasets are provided by [VideoPose3D](https://github.com/facebookresearch/VideoPose3D) Pavllo et al. [2], which can be downloaded by the following steps:

```
cd data
wget https://dl.fbaipublicfiles.com/video-pose-3d/data_2d_h36m_cpn_ft_h36m_dbb.npz
wget https://dl.fbaipublicfiles.com/video-pose-3d/data_2d_h36m_detectron_ft_h36m.npz
cd ..
```

GT 2D keypoints for Human3.6M datasets are provided by [SemGCN](https://github.com/garyzhao/SemGCN) Zhao et al. [3], which can be downloaded by the following steps:
```
cd data
pip install gdown
gdown https://drive.google.com/uc?id=1Ac-gUXAg-6UiwThJVaw6yw2151Bot3L1
python prepare_data_h36m.py --from-archive h36m.zip
cd ..
```
After this step, you should end up with two files in the data directory: data_3d_h36m.npz for the 3D poses, and data_2d_h36m_gt.npz for the ground-truth 2D poses.


### Training from scratch
To train the model, run the following commands.
For HOIF-Net on ground truth input:
```
python main_gcn.py --keypoints gt
```
By default the application runs in training mode. This will train a new model for 50 epochs, using ground truth 2D detections.
If you want to try different network settings, please refer to [`main_gcn.py`](main_gcn.py) for more details. Note that the 
default setting of hyper-parameters is used for training model with CPN detectors as input, please refer to the paper for implementation details.  

For training and evaluating models using 2D detections generated by the Cascaded Pyramid Network, add `--keypoints cpn_ft_h36m_dbb` to the commands:
```
python main_gcn.py --keypoints cpn_ft_h36m_dbb
python main_gcn.py --evaluate ${CHECKPOINT_PATH} --keypoints cpn_ft_h36m_dbb

```
### Visualization
You can generate visualizations of the model predictions by running:
```
python viz.py --architecture gcn --evaluate ${CHECKPOINT_PATH} --viz_subject S11 --viz_action Walking --viz_camera 0 --viz_output output.gif --viz_size 3 --viz_downsample 2 --viz_limit 60
```
The script can also export MP4 videos and supports a variety of parameters (e.g. downsampling/FPS, size, bitrate). See [`viz.py`](viz.py) for more details.

### References

[1] Martinez et al. [A simple yet effective baseline for 3d human pose estimation](https://arxiv.org/pdf/1705.03098.pdf). ICCV 2017.

[2] Pavllo et al. [3D human pose estimation in video with temporal convolutions and semi-supervised training](https://arxiv.org/pdf/1811.11742.pdf). CVPR 2019.

[3] Zhao et al. [Semantic Graph Convolutional Networks for 3D Human Pose Regression](https://arxiv.org/pdf/1904.03345.pdf). CVPR 2019.


## Acknowledgement
This code is extended from the following repositories.
- [3d-pose-baseline](https://github.com/una-dinosauria/3d-pose-baseline)
- [3d_pose_baseline_pytorch](https://github.com/weigq/3d_pose_baseline_pytorch)
- [VideoPose3D](https://github.com/facebookresearch/VideoPose3D)
- [Semantic GCN](https://github.com/garyzhao/SemGCN)
- [High-order GCN](https://github.com/ZhimingZo/HGCN.git)

Thank the authors for releasing their codes. Please also consider citing their works.

