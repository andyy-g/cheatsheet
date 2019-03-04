# CHEATSHEET - ACTIVE STORAGE

## ⚙️ SETUP

* `rails active_storage:install`
* `rails db:migrate`

  *Creation of two tables : __active_storage_blobs__ and __active_storage_attachments__*
* Declare your service in config/storage.yml (<https://guides.rubyonrails.org/active_storage_overview.html#amazon-s3-service>)

## 🔧 MODEL

In your model, add :
* `has_one_attached :elementname` for one-to-one relationship (each record can have one file attached to it),
* `has_many_attached :elementname` for one-to-many relationship (each record can have many files attached to it).

## 👁 VIEW

* To add a field in yout form :
```
  <%= form.label :elementname %>
  <%= form.file_field :elementname %>
```
* To display the image :
```
<%= image_tag(@item.elementname) %>
```
