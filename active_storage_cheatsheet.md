# CHEATSHEET - ACTIVE STORAGE WITH AWS S3

## ⚙️ SETUP

* `rails active_storage:install`
* `rails db:migrate`

  *Creation of two tables : __active_storage_blobs__ and __active_storage_attachments__*
* Sign in on your AWS account, create a bucket and get your Access key ID and Secret access key following [this tutorial][tuto active storage]. 
* Add `gem dotenv-rails` to your Gemfile, bundle install, then create a .env file, put `.env` in your .gitignore, and finally put your API key in your .env file :
```
AMAZON_ACCESS_KEY_ID= 'AKIAKOB5SEYHW8APSIYQ'
AMAZON_SECRET_ACCESS_KEY= 'vCxbJWzclEJCLqZ4zXcSBgT5i9mAQCYMSw1zXyu'
```
* Declare your service in config/storage.yml :
  ```
  amazon:
    service: S3
    access_key_id: <% ENV['AMAZON_ACCESS_KEY_ID'] %>
    secret_access_key: <% ENV['AMAZON_SECRET_ACCESS_KEY'] %>
    region: eu-west-3
    bucket: your-bucket-name
 ```
 Don't forget the `<% %>` 😉
* In config/environments/production.rb:42, indicate your service :
  `config.active_storage.service = :amazon`
* Add `gem "aws-sdk-s3", require: false` to your Gemfile and bundle install.
* Set the AWS secrets with `EDITOR="mate --wait" rails credentials:edit`

  *This will create two files in your config directory : __credentials.yml.enc__ and __master.key__*
* Indicate your keys to heroku, either in your dashboard heroku ('Reveal Config Vars' in the Settings of your app), or in your terminal with the line command `heroku config:set AMAZON_ACCESS_KEY_ID=AKIAKOB5SEYHW8APSIYQ` and `heroku config:set AMAZON_SECRET_ACCESS_KEY=vCxbJWzclEJCLqZ4zXcSBgT5i9mAQCYMSw1zXyu`

## 🔧 MODEL

In your model, add :
* `has_one_attached :elementname` for one-to-one relationship (each record can have one file attached to it),
* `has_many_attached :elementname` for one-to-many relationship (each record can have many files attached to it).

## 👁 VIEW

* To add a field in your form_for :
```ruby
  <%= f.label :elementname %>
  <%= f.file_field :elementname %>
```
* To display the image :
```ruby
<% if @item.elementname.attached? %>
  <%= image_tag(@item.elementname), alt: 'Element Name' %>
<% end %>
```

[tuto active storage]: https://medium.com/alturasoluciones/setting-up-rails-5-active-storage-with-amazon-s3-3d158cf021ff
