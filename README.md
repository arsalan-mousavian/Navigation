# semantic_nav

**Visual Representation for Semantic Target Driven Navigation**

Arsalan Mousavian, Alexander Toshev, Marek Fiser, Jana Kosecka, James Davidson

arXiv 2018

<div align="center">
  <table style="width:100%" border="0">
    <tr>
      <td align="center"><img src='https://cs.gmu.edu/~amousavi/gifs/smaller_fridge_2.gif'></td>
      <td align="center"><img src='https://cs.gmu.edu/~amousavi/gifs/smaller_tv_1.gif'></td>
    </tr>
    <tr>
      <td align="center">Target: Fridge</td>
      <td align="center">Target: Television</td>
    </tr>
    <tr>
      <td align="center"><img src='https://cs.gmu.edu/~amousavi/gifs/smaller_microwave_1.gif'></td>
      <td align="center"><img src='https://cs.gmu.edu/~amousavi/gifs/smaller_couch_1.gif'></td>
    </tr>
    <tr>
      <td align="center">Target: Microwave</td>
      <td align="center">Target: Couch</td>
    </tr>
  </table>
</div>



ArXiv: [https://arxiv.org/abs/1805.06066](https://arxiv.org/abs/1805.06066)


## 1. Installation

### Requirements

#### Python Packages

```shell
networkx
gin-config
```

### Download semantic_nav

```shell
git clone --depth 1 https://github.com/tensorflow/models.git
```

## 2. Datasets

### Download ActiveVisionDataset 
We used Active Vision Dataset (AVD) which can be downloaded from <a href="http://cs.unc.edu/~ammirato/active_vision_dataset_website/">here</a>. To make our code faster and have less memory requirement we created the AVD Minimal dataset which consists of lower resolution images in addition to annotations for target views, predicted object detections, and predicted semantic segmentation. Our code uses AVD Minimal. 

### ActiveVisionDataset Demo


If you wish to navigate the environment, to see how the AVD looks like you can use the following command:
```shell
python viz_active_vision_dataset_main -- \
  --mode=human \
  --gin_config=envs/configs/active_vision_config.gin \
  --gin_params='ActiveVisionDatasetEnv.dataset_root=$AVD_DIR
```

## 3. Training
Right now, the released version only supports training and inference using the real data from Active Vision Dataset.

### Run training
Use the following command for training:
```shell
# Train
python train_supervised_active_vision.py \
  --mode='train' \
  --logdir=$CHECKPOINT_DIR \
  --modality_types='image' \
  --batch_size=8 \
  --train_iters=200000 \
  --lstm_cell_size=2048 \
  --policy_fc_size=2048 \
  --sequence_length=20 \
  --max_eval_episode_length=100 \
  --test_iters=194 \
  --gin_config=envs/configs/active_vision_config.gin \
  --gin_params='ActiveVisionDatasetEnv.dataset_root=$AVD_DIR \
  --logtostderr
```

### Run Evaluation
Use the following command for unrolling the policy on the eval environments. The inference code periodically check the checkpoint folder for new checkpoints to use it for unrolling the policy on the eval environments. After each evaluation, it will create a folder in the $CHECKPOINT_DIR/evals/$ITER where $ITER is the iteration number at which the checkpoint is stored.
```shell
# Eval
python train_supervised_active_vision \
  --mode='eval' \
  --logdir=$CHECKPOINT_DIR \
  --modality_types='det' \
  --batch_size=8 \
  --train_iters=200000 \
  --lstm_cell_size=2048 \
  --policy_fc_size=2048 \
  --sequence_length=20 \
  --max_eval_episode_length=100 \
  --test_iters=194 \
  --gin_config=envs/configs/active_vision_config.gin \
  --gin_params='ActiveVisionDatasetEnv.dataset_root=$AVD_DIR \
  --logtostderr
"""
```
At any point, you can run the following command to compute statistics such as success rate over all the evaluations so far. It also generates gif images for unrolling of the best policy.
```shell
# Visualize and Compute Stats
python viz_active_vision_dataset_main.py \
   --mode=eval \ 
   --eval_folder=$CHECKPOINT_DIR/evals/ \
   --output_folder=$OUTPUT_GIFS_FOLDER \
   --gin_config=envs/configs/active_vision_config.gin \
   --gin_params='ActiveVisionDatasetEnv.dataset_root=$AVD_DIR
```
## Reference
If you find our work useful in your research please consider citing our paper:

```
@inproceedings{MousavianArxiv18,
  author = {A. Mousavian and A. Toshev and M. Fiser and J. Kosecka and J. Davidson},
  title = {Visual Representations for Semantic Target Driven Navigation},
  booktitle = {arXiv:1805.06066},
  year = {2018},
}
```

