#Wrapper of Cropbox for Yii

This is widget wrapper and fork of Cropbox https://github.com/hongkhanh/cropbox . This widget allows crop image before upload to server and send informations about crop in JSON format.

###Required

- YiiBooster version 3.0.1 https://github.com/clevertech/YiiBooster/tree/3.0.1

###Uses

1. Download widget in extensions folder.
2. Download Yii-image extension from https://bitbucket.org/z_bodya/yii-image and setting it.
3. Add to your view following code: 
```php
$this->widget('ext.cropbox.CropboxWidget', array(
    'form' => $form, //CActiveForm
    'model' => $model, //CActiveRecord
    'attribute' => 'file_upload', //Name of attribute for field type of file
    'attributeCropInfo' => 'crop_info', //Name of field where will transferred informations about crop image
    'thumbUrl' => //URL to thumbnail of image before crop new image (old image)
    'originalUrl' => //URL to image for display fullscreen after click at thumbnail (old image)
));
```4. Add to your model following code:

Add property file_upload and crop_info:
```php
public $file_upload;
public $crop_info;

public function rules()
{
  ...
  array(
      'file_upload', 
      'file',
      'types' => array('jpg', 'png', 'gif', 'jpeg'),
      'mimeTypes' => array('image/gif', 'image/jpeg', 'image/png', 'image/pjpeg'),
  ),
  array('crop_info', 'filter', 'filter' => function($value) { 
      return json_decode($value);
  }),
  ...
}
```

Add in method beforeSave:
```php
if ($file = CUploadedFile::getInstance($this, 'file_upload'))
{
    $pathSave = Yii::getPathOfAlias('path.to.save');
    $fileName = md5(microtime() + rand(0, 1000)) . '.' . $file->getExtensionName();

    // original image
    $image = Yii::app()->image->load($file->getTempName());
    $image->save($pathSave . '/' . $fileName);

    // thumb image
    // size of thumb image, by default 200x200px
    $width = $height = 200;
    $image
        ->resize((int)$this->crop_info->dw, (int)$this->crop_info->dh)
        ->crop($width, $height, abs($this->crop_info->y), abs($this->crop_info->x))
        ->save($pathSave . '/_' . $fileName);
}
```

###Links

- **YiiBooster 3.0.1** - https://github.com/clevertech/YiiBooster/tree/3.0.1
- **Yii-Image** - https://bitbucket.org/z_bodya/yii-image
- **Cropbox** - https://github.com/hongkhanh/cropbox
- **My blog** - http://mihaly4.ru
