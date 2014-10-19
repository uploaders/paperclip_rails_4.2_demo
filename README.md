# Paperclip Example with Rails 4.2

###Credits:
* [Pete Mitchell](https://github.com/petemmitchell)
* [Deirdre Allison](https://github.com/deirdreallison)
* [Paperclip documentation](https://github.com/thoughtbot/paperclip)
* [Paperclip Demo](https://github.com/thoughtbot/paperclip_demo)

###Additional gems you will need:
* `gem 'rails', '~> 4.2.0.beta2'`
* `gem 'aws-sdk'`
* `gem 'dotenv-rails'`
* `gem 'paperclip'`

###Set up Paperclip:

**Follow Paperclip documentation**:

* Paperclip requires ImageMagick
```brew install imagemagick``` for OSX

* Edit Model
```
class User < ActiveRecord::Base
  has_attached_file :avatar, styles: { medium: '300x300>', thumb: '100x100>' }, default_url: '/images/:style/missing.png'
  validates_attachment_content_type :avatar, content_type: /\Aimage\/.*\Z/
end
```

* Add migration for Avatar
```rails generate paperclip user avatar```
should look like this:
```
class AddAttachmentAvatarToUsers < ActiveRecord::Migration
  def self.up
    change_table :users do |t|
      t.attachment :avatar
    end
  end

  def self.down
    remove_attachment :users, :avatar
  end
end
```

* Update Edit and New views or form partial to include this line:
```  <%= f.file_field :avatar %>```

* Add Avatar to strong params in Controller:
```
 def user_params
    params.require(:user).permit(:name, :avatar)
  end
  ```

* Add to User show view:
```<%= image_tag @user.avatar.url(:medium) %>```


###Configure S3 (requires S3 Bucket from AWS)

Add environment variables (see .sample_env):
```
S3_BUCKET_NAME: yourbucketname
AWS_ACCESS_KEY_ID: youraccesskeyhere
AWS_SECRET_ACCESS_KEY: yoursecretkeyhere
```

In config/environments add the following in each environment you want image uploading to S3 to work in:
 ```
  config.paperclip_defaults = {
    storage: :s3,
    s3_credentials: {
      bucket: ENV['S3_BUCKET_NAME'],
      access_key_id: ENV['AWS_ACCESS_KEY_ID'],
      secret_access_key: ENV['AWS_SECRET_ACCESS_KEY']
    }
  }

  Paperclip.options[:command_path] = '/usr/local/bin/'
  ```

And you are ready to upload images to your Rails 4.2 app! Check out the example
screenshots below. Happy coding!

**Uploading Image**
![Uploading local image](http://dl.dropbox.com/s/qb5y8xsok9s1a85/Screen%20Shot%202014-10-19%20at%203.20.59%20PM.png?dl=0)

**Confirmation**
![Image uploaded](http://dl.dropbox.com/s/pdu56vqdas238be/Screen%20Shot%202014-10-19%20at%203.21.31%20PM.png?dl=0)
