------
title: yii2 curd
date: 2018-06-14 22:13:52
categories: yii2
tags:
    - yii2
    - curd
------

1. controler

```php
use Yii\models\Admin;

public function actionChangeEmail{
    $this->layout = 'layout1';

    $model = Admin::find()->where('adminuser=:user',[':user'=>Yii::$app->session['admin']['adminuser']])->one();

    if( Yii::$app->request->isPost){
        $post = Yii::$app0>request->post();
        if($model->changeemail($post)){
            Yii::$app->session->setFlash('info','update success!');
        }
        return $this->render('changeemail', ['model' => $model]);
    }

}

```

2. model

```php

public function rules(){
    return [
        ['adminuser', 'required', 'message'=>'username cannot be empty', 'on' => 'changeemail'],
        ['adminpass', 'required', 'message'=>'wordpass cannot be empty', 'on' => 'changeemail'],
        ['remember', 'bool', 'on' => 'login'],
        ['adminemial', 'email', 'message' => 'emial type error'],
        ['adminemial', 'unique', 'message' => 'email aready exists!'],
    ];

}


function changeemail($data){
    $this->scenario = 'changeemail';
    if($this->load($data) && $this->validate()){
        return (bool)$this->updateAll(['adminemail'=>$this->adminemail], 'adminuser = :user',[':user'=> Yii::$app->user]);
    }
    return false;
}



```

3. view

```html
<?php
use yii\bootstrap\ActiveForm;
use yii\helpers\Url;
use yii\helpers\Html;
?>

<?=Url::to(['default/changeemail'])?>

<?php

 if( Yii::$app->session->hasFlash('info')){
    echo Yii::$app->session->getFlash('info');
 }

 $form= ActiveForm::begin([
    'option' => ['class' => 'new_user_form'],
    'fieldConfig' => [
        'template' => '<div clas="span12 field-box">{label}{input}</div>{error}'
    ]
 ]);
?>

<?php echo $form->field($model, 'adminuser')->textInput(['class'=>'col-md-3', 'disabeled'=>true]); ?>
<?php echo $form->field($model, 'adminpass')->passwordInput(['class'=>'col-md-3']); ?>
<?php echo $form->field($model, 'adminemail')->textInput(['class'=>'col-md-3']); ?>

<div class="col-md-12 actions"?>
<?= Html::submitButton('create', ['class'=>'btn btn-success'])?>
<span>or</span>
<?= Html::resetButton('create', ['class'=>'btn btn-default'])?>
</div>

<?php ActiveForm::end();?>




```
