---
layout: post
title: Model validation must implement interface Phalcon\ValidationInterface
category: [战Error]
date: 2016-07-24
---
给新的线上服务器装phalcon3.0，在Model validation这里就报错了，想必是升级后一部分不向下兼容了，查了查更新log，果然有这部分代码更换了。
		
	Catchable fatal error: Argument 1 passed to Phalcon\Mvc\Model::validate() must implement interface Phalcon\ValidationInterface, instance of Phalcon\Mvc\Model\Validator\Email given in C:\xampp\htdocs\invo\app\models\Users.php on line 13


<!-- more -->
引用官网blog:
	
	Phalcon\Mvc\Model\Validation is now deprecated in favor of Phalcon\Validation. The functionality of both components is merged into one, allowing us to reduce the codebase while offering the same functionality as before.
	Previously validations were implemented as follows:
	
	namespace Invo\Models;
	
	use Phalcon\Mvc\Model;
	use Phalcon\Mvc\Model\Validator\Email as EmailValidator;
	use Phalcon\Mvc\Model\Validator\Uniqueness as UniquenessValidator;

	class Users extends Model
	{
	    public function validation()
	    {
		$this->validate(
		    new EmailValidator(
		        [
		            'field' => 'email',
		        ]
		    )
		);

		$this->validate(
		    new UniquenessValidator(
		        [
		            'field'   => 'username',
		            'message' => 'Sorry, That username is already taken',
		        ]
		    )
		);

		if ($this->validationHasFailed() == true) {
		    return false;
		}
	    }
	}
	
	
	Introducing Phalcon\Validation, you will need to change the above to:
	
	namespace Invo\Models;

	use Phalcon\Mvc\Model;
	use Phalcon\Validation;
	use Phalcon\Validation\Validator\Email as EmailValidator;
	use Phalcon\Validation\Validator\Uniqueness as UniquenessValidator;

	class Users extends Model
	{
	    public function validation()
	    {
		$validator = new Validation();

		$validator->add(
		    'email', //your field name
		    new EmailValidator([
		        'model' => $this,
		        "message" => 'Please enter a correct email address'
		    ])
		);

		$validator->add(
		    'username',
		    new UniquenessValidator([
		        'model' => $this,
		        'message' => 'Sorry, That username is already taken',
		    ])
		);

		return $this->validate($validator);
	    }
	}

已经不支持以前的那种validation方式了，validator的位置变了，添加validation方式也变成了先创建一个validation对象，然后调用add方法来添加需要验证的字段。
