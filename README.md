# yolov3-ios
Using yolo v3 object detection on ios platform.

## Example applications:
![car](https://raw.githubusercontent.com/Mrlawrance/yolov3-ios/master/imgfolder/car.jpeg)

## QuickStart:
Run tiny_model.xcodeproj in ios.

## Training
The training process mainly consults [qqwweee/keras-yolo3](https://github.com/qqwweee/keras-yolo3). We add yolov3 with [Densnet](https://arxiv.org/pdf/1608.06993.pdf).

### 1.Requirement
* python 3.6.4
* keras 2.1.5
* tensorflow 1.6.0

### 2.Generate datasets
Generate datasets with VOC format. And try ```python voc_annotations```.

### 3.Start training
* ```cd yolov3_with_Densenet```

For yolo model with darknet:
* ```wget https://pjreddie.com/media/files/darknet53.conv.74```
* rename it as darknet53.weights
* ```python convert.py -w darknet53.cfg darknet53.weights model_data/darknet53_weights.h5```
* ```python yolov3_train.py```, with model_data/darknet53_weights.h5 as pre-trained model

For yolo model with densenet：
* ```python densenet_train.py```, with model_data/dense121_weights.h5 as pre-trained model


## Converting
### 1.Building environment
```
virtualenv -p /usr/bin/python2.7 keras_coreml_virt
source keras_coreml_virt/bin/activate
pip install protobuf
pip install tensorflow==1.6.0
pip install keras==2.1.5
pip install h5py
pip install coremltools==0.8.0
```

### 2.Convert .h5 model to .mlmodel
```python coreml.py```


## Building project in Xcode

* open tiny_model.xcodeproj with Xcode 9+
* change the .mlmodel file and Target Menmbership

For yolo model with darknet or densenet
* modify the code from line 43 to line 49 in YOLO.swift
* change the labels and the anchors in Helpers.swift
* run the project

For tiny model
* just change the labels and run the project

convert.py içindeki 
 input_layer = Input(shape=(None, None, 3))
 input_layer = Input(shape=(416,416,3)) ile değiştiriyoruz. 
 
 Daha sonra 
 python convert.py Yeni_model.cfg Yeni_modelin_agirlik_dosyasi.weights model_data/output_dosya_ismi.h5
 komutunu bulunduğu klasörden çağırıyoruz. Bu weights dosyasını keras dosyasına çeviriyor. Dikkat!!! Bu komut yukarıdakinden farklı!!!!
 
 Çıkan output dosyasini coreml.py'ın bulunduğu klasöre götürerek
 coreml.py dosyasını elimizdeki dosyaya göre editliyoruz. 
 Bu keras dosyasını coreml dosyasına çeviriyor.
 
 Sonrasında
 32 bit modeli 16bit'e çevirmek için aşağıdaki kodları çalıştırıyoruz.
 
 import coremltools
 model_spec = coremltools.utils.load_spec('yukarıdaki işlemden çıkan *.mlmodel uzantılı dosyanın path'i')
 model = coremltools.models.MLModel('yukarıdaki işlemden çıkan *.mlmodel uzantılı dosyanın path'i')
 coremltools.utils.convet_neural_network_to_fp16(model).save('16bit çıkacak olan *.mlmodel dosyasına isim veriyoruz')
 
 Bu dosyayı bu projenin içindeki xcode projesine ekleyerek. YOLO.swift içinden erişimini sağlıyoruz. tiny.mlmodel yazan kodu bir yukarda hangi ismi verdiysek onunla değiştiriyoruz. Helpers.swift içinden anchor ve label değişimlerini yapabiliriz. 
 
