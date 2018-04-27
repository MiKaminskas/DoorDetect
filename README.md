# DoorDetect
TensorFlow test on door detection

training command:
python3 train.py --logtostderr --train_dir=training/ --pipeline_config_path=training/ssd_mobilenet_v1_pets.config

eval command:
python3 eval.py --logtostderr --train_dir=training/ --pipeline_config_path=training/ssd_mobilenet_v1_pets.config --checkpoint_dir=training/ --eval_dir=training/


export model:
python3 export_inference_graph.py --input_type image_tensor --pipeline_config_path=training/ssd_mobilenet_v1_pets.config --trained_checkpoint_prefix=training/model.ckpt-29830 --output_directory ~/Documents/
