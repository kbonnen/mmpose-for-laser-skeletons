# Using mmpose on  kid data at UT.

## install mmpose
https://mmpose.readthedocs.io/en/latest/installation.html

Step 0. Download and install Miniconda from the official website.

Step 1. Create a conda environment and activate it.

	conda create --name openmmlab python=3.8 -y
	conda activate openmmlab

Step 2. Install PyTorch following official instructions, e.g.

	conda install pytorch torchvision cpuonly -c pytorch

Step 3. Install MMEngine and MMCV using MIM.

	pip install -U openmim
	mim install mmengine
	mim install "mmcv>=2.0.1"
	mim install "mmdet>=3.1.0"


Step 4. 

Install as a Python package
To use mmpose as a dependency or third-party package, install it with pip:
	mim install "mmpose>=1.1.0"

But also clone the github repository so you can use their demos directly
	git clone https://github.com/open-mmlab/mmpose.git

## verify installation
Step 1. We need to download config and checkpoint files.
	mim download mmpose --config td-hm_hrnet-w48_8xb32-210e_coco-256x192  --dest .



## Demos
https://mmpose.readthedocs.io/en/latest/demos.html

### 2D human whole body demo
https://mmpose.readthedocs.io/en/latest/demos.html#d-human-whole-body-pose-top-down-video-demo



command

```python demo/topdown_demo_with_mmdet.py \
    ${MMDET_CONFIG_FILE} ${MMDET_CHECKPOINT_FILE} \
    ${MMPOSE_CONFIG_FILE} ${MMPOSE_CHECKPOINT_FILE} \
    --input ${INPUT_PATH} \
    [--output-root ${OUTPUT_DIR}] [--save-predictions] \
    [--show] [--draw-heatmap] [--device ${GPU_ID or CPU}] \
    [--bbox-thr ${BBOX_SCORE_THR}] [--kpt-thr ${KPT_SCORE_THR}] 
    
    ```

mmpose example (with small modification to use cpu)

```python demo/topdown_demo_with_mmdet.py \
    demo/mmdetection_cfg/rtmdet_m_640-8xb32_coco-person.py \
    https://download.openmmlab.com/mmpose/v1/projects/rtmpose/rtmdet_m_8xb32-100e_coco-obj365-person-235e8209.pth \
    configs/wholebody_2d_keypoint/topdown_heatmap/coco-wholebody/td-hm_hrnet-w48_dark-8xb32-210e_coco-wholebody-384x288.py \
    https://download.openmmlab.com/mmpose/top_down/hrnet/hrnet_w48_coco_wholebody_384x288_dark-f5726563_20200918.pth \
    --input https://user-images.githubusercontent.com/87690686/137440639-fb08603d-9a35-474e-b65f-46b5c06b68d6.mp4 \
    --output-root vis_results/ --device=cpu
    ```


example that Kate ran on Sara's data (don't worry about the stuff before --input)
```python demo/topdown_demo_with_mmdet.py \
    demo/mmdetection_cfg/rtmdet_m_640-8xb32_coco-person.py \
    https://download.openmmlab.com/mmpose/v1/projects/rtmpose/rtmdet_m_8xb32-100e_coco-obj365-person-235e8209.pth \
    configs/wholebody_2d_keypoint/topdown_heatmap/coco-wholebody/td-hm_hrnet-w48_dark-8xb32-210e_coco-wholebody-384x288.py \
    https://download.openmmlab.com/mmpose/top_down/hrnet/hrnet_w48_coco_wholebody_384x288_dark-f5726563_20200918.pth \
    --input ~/Downloads/part-child.mp4 \
    --output-root vis_results_2d/ --device=cpu --save-predictions
    ```


### 3D human bodies
command

```python  demo/body3d_pose_lifter_demo.py  \
${MMDET_CONFIG_FILE} \
${MMDET_CHECKPOINT_FILE} \
${MMPOSE_CONFIG_FILE_2D} \
${MMPOSE_CHECKPOINT_FILE_2D} \
${MMPOSE_CONFIG_FILE_3D} \
${MMPOSE_CHECKPOINT_FILE_3D} \
--input ${VIDEO_PATH or IMAGE_PATH or 'webcam'} \
[--show] \
[--disable-rebase-keypoint] \
[--disable-norm-pose-2d] \
[--num-instances ${NUM_INSTANCES}] \
[--output-root ${OUT_VIDEO_ROOT}] \
[--save-predictions] \
[--device ${GPU_ID  or  CPU}] \
[--det-cat-id ${DET_CAT_ID}] \
[--bbox-thr ${BBOX_THR}] \
[--kpt-thr ${KPT_THR}] \
[--use-oks-tracking] \
[--tracking-thr ${TRACKING_THR}] \
[--show-interval ${INTERVAL}] \
[--thickness ${THICKNESS}] \
[--radius ${RADIUS}] \
[--online]
```


NOTES:
1. ${VIDEO_PATH} can be the local path or URL link to video file.
2. If the [--online] option is set to True, future frame information can not be used when using multi frames for inference in the 2D pose detection stage.

example
```python demo/body3d_pose_lifter_demo.py  \
demo/mmdetection_cfg/rtmdet_m_640-8xb32_coco-person.py \
https://download.openmmlab.com/mmpose/v1/projects/rtmpose/rtmdet_m_8xb32-100e_coco-obj365-person-235e8209.pth \
configs/body_2d_keypoint/rtmpose/body8/rtmpose-m_8xb256-420e_body8-256x192.py \
https://download.openmmlab.com/mmpose/v1/projects/rtmposev1/rtmpose-m_simcc-body7_pt-body7_420e-256x192-e48f03d0_20230504.pth \
configs/body_3d_keypoint/video_pose_lift/h36m/video-pose-lift_tcn-243frm-supv-cpn-ft_8xb128-200e_h36m.py \
https://download.openmmlab.com/mmpose/body3d/videopose/videopose_h36m_243frames_fullconv_supervised_cpn_ft-88f5abbb_20210527.pth  \
--input https://user-images.githubusercontent.com/87690686/164970135-b14e424c-765a-4180-9bc8-fa8d6abc5510.mp4 \
--output-root  vis_results \
--save-predictions --device=cpu
```


Kate ran this on your data using her version of body3d_pose_lifter_demo.py which is in our mmpose-laserskeleton repo.

```python demo/body3d_pose_lifter_demo.py \
demo/mmdetection_cfg/rtmdet_m_640-8xb32_coco-person.py \
https://download.openmmlab.com/mmpose/v1/projects/rtmpose/rtmdet_m_8xb32-100e_coco-obj365-person-235e8209.pth \
configs/body_2d_keypoint/rtmpose/body8/rtmpose-m_8xb256-420e_body8-256x192.py \
https://download.openmmlab.com/mmpose/v1/projects/rtmposev1/rtmpose-m_simcc-body7_pt-body7_420e-256x192-e48f03d0_20230504.pth \
configs/body_3d_keypoint/video_pose_lift/h36m/video-pose-lift_tcn-243frm-supv-cpn-ft_8xb128-200e_h36m.py \
https://download.openmmlab.com/mmpose/body3d/videopose/videopose_h36m_243frames_fullconv_supervised_cpn_ft-88f5abbb_20210527.pth \
--input ~/Downloads/part-child-small-compat.mov\
--output-root  vis_results  --device=cpu --online
```

