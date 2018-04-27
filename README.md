# DoorDetect
TensorFlow test on door detection

training command:
python3 train.py --logtostderr --train_dir=training/ --pipeline_config_path=training/ssd_mobilenet_v1_pets.config

eval command:
python3 eval.py --logtostderr --train_dir=training/ --pipeline_config_path=training/ssd_mobilenet_v1_pets.config --checkpoint_dir=training/ --eval_dir=training/


