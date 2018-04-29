# DoorDetect
TensorFlow test on door detection
0. Определится что нужно научится распознавать.
1. Собрать базу из фотографий (~200штук). Размер фотографий 400*400 - 800*800. Чем крупнее фотографии, тем медленее обучение.
2. Отметить на каждой фотографии детектируемый объект. Размечать можно при помощи программы, которая генерирует структуру  XML подобную структуру. Например, такая программа: https://github.com/tzutalin/labelImg . Для каждой фотографии создается по одной XMLки. XML состоит из нескольких ключей. Нам необходимы только ключи xmin, ymin, xmax, ymax и название файла.
3. После отметки всех фотографий необходимо сконвертировать массив xml в два csv файла. Один файл для тренировки, а второй для теста. Пропорции по своему вкусу. Я выбрал 70%(тренировка) на 30(тест). Конвертирование делается при помощи скрипта xml_to_csv.py
4. Далее генерируется файлы tfrecord, с которыми может работать tensorflow. Генерируются при помощи скрипта generate_tfrecord.py. На вход подаются два csv, на выходе два tfrecord.
5. Создать файл с описанием классов на которые идет обучение. Пример класса:
item {
  id: 1
  name: 'door'
}
Нумерация от 1 т.к. ноль зарезервирован для чего-то.  Если классов пять, то и итемов 5.
5. Следующий этап - этап тренировки. Сначала надо выбрать конфиг для тренировки. На выбор разработчики tf предлагается несколько различных https://github.com/tensorflow/models/tree/master/research/object_detection/samples/configs . Можно выбрать самый быстрый и не парится т.к. это быстрее. Надо имезинть параметр num_classes на колличество классов, в моем случае 1. Еще в конфиге можно изменить  batch_size , потому как на машинах с маленьким колличество RAM обучение будет вылетать из-за недостатка памяти.  
6. Выбрать модель обучения. https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md . Модель v1 хорошо подходит для простейшего и быстрого распознавания объектов.
7. Дальше следует определится на чем будем считать. На выбор варианту: CPU, GPU(&&>2GB RAM) и Google Cloud. Лучше выбирать из двух последний вариантов т.к. они значительно быстрее. Я предвкушал проблемы с драйверами видеокарты и поэтому предпочел сразу считать на CPU.
8. После этого надо склонировать репозиторий https://github.com/tensorflow/models .И установить список зависимостей:https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md . Перейти в models/research/object_detection/, скопировать все сгененированные файлы сюда. И после этого запустить 
training command:
python3 train.py --logtostderr --train_dir=training/ --pipeline_config_path=training/ssd_mobilenet_v1_pets.config

9. Обучение бегает долго(В моем случае ушло 15часов). Оценивать точность можно при помощи скрипта, который надо переодически запускать
eval command:
python3 eval.py --logtostderr --train_dir=training/ --pipeline_config_path=training/ssd_mobilenet_v1_pets.config --checkpoint_dir=training/ --eval_dir=training/
Результаты скрипта можно увидеть в браузере после запуска tensorboard --logdir=КАТАЛОГсФАЙЛОМмодель. Когда точности будет достаточно обучение можно остановить.

10. Экспорт модели. Проихводится из того же каталога командой
export model:
python3 export_inference_graph.py --input_type image_tensor --pipeline_config_path=training/ssd_mobilenet_v1_pets.config --trained_checkpoint_prefix=training/model.ckpt-29830 --output_directory ~/Documents/
В итоге получится файл frozen_inference_graph.pb

11. Использование модели в андроид приложении. Для этого надо склонировать https://github.com/tensorflow/tensorflow . Импортировать в андроид-студио проект из tensorflow/tensorflow/examples/android/ 
12. В build.gradle выбрать сборщик cmake.  И в классе DetectorActivity заменить строки путей на YOLO_MODEL_FILE и TF_OD_API_LABELS_FILE. Лейблс это текстовый документ где по строчка размещены классы объектов. В моем случае:
?????
door

13. Запустить и радоватся! 
