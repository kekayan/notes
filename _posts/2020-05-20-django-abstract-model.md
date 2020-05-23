---
toc: true
layout: post
description: The abstract model in django sample use case like auditing
categories: [django,python]
title: Django Abstract Model as Base Model
---

# Django Abstract Model
Let's understand the value of Django abstract model by a sample use case .
Imagine a scenario where we are building a Dog Management App.Let's think our main models are
*   dog
*   owner

So we will create two apps for example `dog` and `owner`
```shell
django-admin startapp dog
django-admin startapp owner
```
While designing the data model, there is a requirement to save with created date and update date for both to audit the creation and modification. Since it is a common best practice .
Usually what you will think, 

for owner, it will be

```python
class Owner(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    name = models.CharField(max_length=150)
    # including  these two fields in each model
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)    

    def __str__(self):
        return self.name

```



and our models for Dog, it will be 
```python
class Dog(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    name = models.CharField(max_length=150)
    owner =models.ForeignKey(Owner, on_delete=models.CASCADE)
    # including  these two fields in each model
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)    

    def __str__(self):
        return self.name

```

 as you noticed we have included the both created date and update date fields for each model .


Someone With the knowledge of OOP will think about it in a different way. With help of Abstract classes in Django
He can first create a Date Aware Abstract model as a base and then extend it to other models without repeating it.
To create a Abstract model in Django we have to add the Meta class and mention abstract is true.

```python
class DateAwareModel(models.Model):

    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True

```

## Why Abstract Model
The abstract Model won't have a table for it self or in other words django won't generate tables for abstract models.So we can use the fields by inheriting it in as many models we want without repeating it.

Now our Dog model will inherit DateAwareModel

```python
class Dog(DateAwareModel):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    name = models.CharField(max_length=150)
   
    def __str__(self):
        return self.name

```
same for the Owner Model in addition to mentioned fields it now have created_at and updated_at from DateAwareModel.

## auto_now & auto_now_add
```python
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```
`auto_now_add` - updates on creation only
`auto_now` -  updated to the current timestamp every time an object is saved or modified.

**Meanwhile if you find any mistake please let me know .** :envelope:  [email](mailto:notkekayan@gmail.com)