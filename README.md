# UniFucGrasp: Human-Hand-Inspired Unified Functional Grasp Annotation Strategy and Dataset for Diverse Dexterous Hands

Functional Hand Gesture Generation for Diverse Robotic Hands

## Prerequisites:
```
conda create -n unifuncgrapnet python=3.8 -y
conda activate unifuncgraspnet 

pip install -r requirements.txt
```
## Get Started

Training

You need to modify the configuration file based on your requirements. Below are the key parameters commonly adjusted in the `config/` folder:

- `train.yaml`
  - `name`: Specify the training model name.
  - `gpu`: Set the GPU ID based on the available GPU device(s).
  - `training/max_epochs`: Define the number of training epochs.
- `model.yaml`
  - The `transformer_dim` defines the embedding size of the transformer, while `hidden_dim` specifies the dimension of the hidden layers in the CVAE network.
- `dataset/cmap_dataset.yaml`
  - `robot_names`: Provide the list of robot names to be used for pretraining.
  - `batch_size`: Set the dataloader batch size as large as possible. 
  - `object_pc_type`: Use `random` for major experiments  This parameter should remain the same during training and validation.

After updating the config file, simply run:

```
python train.py
```

## Validation

Run `python validate.py` simply using the test dataset and the trained model parameters.

```
python validate.py
```

## Dataset

You can download our prepared dataset here,For detailed information about the dataset, please refer to the paper.



## Steps to Apply our Method to a New Hand

1. Modify your hand's URDF. You can refer to an existing URDF file for guidance on making modifications.
2. Add the hand's URDF and mesh paths to `data/data_hand.Then, add the corresponding information in the `hand_asset.json` file.Generate the hand model from the URDF and mesh files created according to `HandModel/handmodel.py`.
3. Specify redundant  remove_links names in `hand_assert.json . You can visualize the links  to identify which links are irrelevant for contact.
4. Use `data_utils/generate_pc.py` to sample point clouds for each robot link and save them.
5. Then, simply replace the `robot_name` in `configs/dataset/dataset.yaml` with the corresponding hand model name.




# Functional Grasp Dataset Collection via Human-to-Robot Hand Mapping

## Requirements

- **URDF and XML files**: Required to define the robotic hand and its joints.  
- **MuJoCo**: For simulation and real-time interaction between the robotic hand and objects.  
- **Realsense Camera**: Intel RealSense D415 or D435 for capturing human hand gestures.  
- **Python Packages**: `numpy`, `pybullet` or `mujoco-py`, `Open3D`, etc.  

## Key Scripts

### 1. `Human_Joint_Collect.py`
- Captures human hand joint angles using the Realsense camera.  
- Uses the K2J (Keypoint-to-Joint) module to convert keypoints to joint angles.  
- Outputs joint angle sequences for gesture mapping.
```
python Human_Joint_Collect.py

```

### 2. `RobotHand_Control.py`
- Maps human hand joint angles to the robotic hand, considering its DOFs, kinematic constraints, and joint-to-motor space mapping.  
- Actively controls the robotic hand in MuJoCo simulation to interact with objects.  
- Records contact points, forces, and hand joint states.
```
python RobotHand_Control.py

```
### 3. `Data_Postprocess.py` 
- Filters raw captured data to extract stable and functionally reliable grasps.  
- Performs geometry-based force-closure analysis to validate grasp quality.  
- Prepares the final dataset for training or evaluation.
```
python Data_Postprocess.py

```
## Notes

- Ensure that MuJoCo is installed and properly licensed before running simulations.  
- Use a Realsense D415 or D435 camera to achieve accurate human hand joint capture.  
- The mapping accounts for the mechanical DOFs and structural differences of each hand, ensuring human-like grasp behavior.  
- For hands with fewer DOFs than a human hand (e.g., InspireHand), sparse mapping and scaling are applied to adapt human gestures.
