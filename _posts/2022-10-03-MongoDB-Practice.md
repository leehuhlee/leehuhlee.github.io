---
layout: post
title: "[MongoDB] MongoDB Practice"
date: 2022-10-03
excerpt: "MongoDB"
tags: [MongoDB, DB, NoSQL]
comments: false
---

# IDE

## Download

  - Download MongoDB for VSCode in Visual Studio Code

<figure>
  <a href="/assets/img/posts/mongodb_practice/0.jpg"><img src="/assets/img/posts/mongodb_practice/0.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>


## Create Cluster
  - Create Cluster on <a href="https://www.mongodb.com/">MongoDB Web</a>

<figure>
  <a href="/assets/img/posts/mongodb_practice/1.jpg"><img src="/assets/img/posts/mongodb_practice/1.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>

# Connection

  - Click Connection button on Mongo DB web and check your cluster connection string.
  - Click MongoDb Playground on side bar.
  - Connect your cluster in VSCode with your connection string.

<figure class="third">
  <a href="/assets/img/posts/mongodb_practice/2.jpg"><img src="/assets/img/posts/mongodb_practice/2.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/3.jpg"><img src="/assets/img/posts/mongodb_practice/3.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/4.jpg"><img src="/assets/img/posts/mongodb_practice/4.jpg"></a>
  <figcaption>Connection</figcaption>
</figure>

  - Change the name

<figure>
  <a href="/assets/img/posts/mongodb_practice/5.jpg"><img src="/assets/img/posts/mongodb_practice/5.jpg"></a>
  <figcaption>Connection</figcaption>
</figure>

## Playground

  - Create new Playground to add your script.

<figure>
  <a href="/assets/img/posts/mongodb_practice/6.jpg"><img src="/assets/img/posts/mongodb_practice/6.jpg"></a>
  <figcaption>Connection</figcaption>
</figure>

# IRUD

## Insert

  - `use` switch current database.
  - `db.{collection}` access to the collection.
  - `insertOne` insert one document on your collection.

{% highlight mongodb %}
use('video');

var movie = {"title" : "Star Wars: Episode IV - A New Hope" ,
    "director" : "George Lucas",
    "year" : 1977};

db.movies.insertOne(movie);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mongodb_practice/7.jpg"><img src="/assets/img/posts/mongodb_practice/7.jpg"></a>
  <figcaption>IRUD</figcaption>
</figure>

  - `insertMany` insert many document on your collection.

{% highlight mongodb %}
db.movies.insertMany([{"title" : "Ghostbusters"}, 
    {"title" : "E.T"}, 
    {"title" : "Blade Runner"}]);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mongodb_practice/13.jpg"><img src="/assets/img/posts/mongodb_practice/13.jpg"></a>
  <figcaption>IRUD</figcaption>
</figure>

## Read

  - `find` shows all documents.

{% highlight mongodb %}
db.movies.find().pretty();
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mongodb_practice/8.jpg"><img src="/assets/img/posts/mongodb_practice/8.jpg"></a>
  <figcaption>IRUD</figcaption>
</figure>

  - `findOne` shows only one document.

{% highlight mongodb %}
db.movies.findOne();
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mongodb_practice/9.jpg"><img src="/assets/img/posts/mongodb_practice/9.jpg"></a>
  <figcaption>IRUD</figcaption>
</figure>

## Update

  - `updateOne` update one document.

{% highlight mongodb %}
db.movies.updateOne({title : "Star Wars: Episode IV - A New Hope"}, 
    {$set : {reviews:[]}});
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mongodb_practice/10.jpg"><img src="/assets/img/posts/mongodb_practice/10.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/11.jpg"><img src="/assets/img/posts/mongodb_practice/11.jpg"></a>
  <figcaption>IRUD</figcaption>
</figure>

## Delete

  - `deleteOne` delete one document.
  
{% highlight mongodb %}
db.movies.deleteOne({title : "Star Wars: Episode IV - A New Hope"});
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mongodb_practice/12.jpg"><img src="/assets/img/posts/mongodb_practice/12.jpg"></a>
  <figcaption>IRUD</figcaption>
</figure>

  - `deleteMany` delete many documents.

<figure class="half">
  <a href="/assets/img/posts/mongodb_practice/14.jpg"><img src="/assets/img/posts/mongodb_practice/14.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/15.jpg"><img src="/assets/img/posts/mongodb_practice/15.jpg"></a>
  <figcaption>IRUD</figcaption>
</figure>

  - `deleteMany({})` can delete all documents.

<figure>
  <a href="/assets/img/posts/mongodb_practice/16.jpg"><img src="/assets/img/posts/mongodb_practice/16.jpg"></a>
  <figcaption>IRUD</figcaption>
</figure>

### Drop

  - `drop` drops the collection.

# Blocking
  
{% highlight mongodb %}
db.dropDatabase = DB.prototype.dropDatabase = no; // block to delete database
DBCollection.prototype.drop = no; // block to delete collection
DBCollection.prototype.dropIndex = no; // block to delete Index
DBCollection.prototype.dropIndexes = no; // block to delete Indexes
{% endhighlight %}

# Document

## Replace

  - `replaceOne` can change database version.

<figure class="half">
  <a href="/assets/img/posts/mongodb_practice/17.jpg"><img src="/assets/img/posts/mongodb_practice/17.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/18.jpg"><img src="/assets/img/posts/mongodb_practice/18.jpg"></a>
  <figcaption>Document</figcaption>
</figure>

## Modifier

  - `$inc` increase field value.

<figure class="half">
  <a href="/assets/img/posts/mongodb_practice/19.jpg"><img src="/assets/img/posts/mongodb_practice/19.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/20.jpg"><img src="/assets/img/posts/mongodb_practice/20.jpg"></a>
  <figcaption>Document</figcaption>
</figure>

  - `$set` set field value.
  - `$unset` remove field from the document.

<figure class="thrid">
  <a href="/assets/img/posts/mongodb_practice/21.jpg"><img src="/assets/img/posts/mongodb_practice/21.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/22.jpg"><img src="/assets/img/posts/mongodb_practice/22.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/23.jpg"><img src="/assets/img/posts/mongodb_practice/23.jpg"></a>
  <figcaption>Document</figcaption>
</figure>

<figure class="half">
  <a href="/assets/img/posts/mongodb_practice/24.jpg"><img src="/assets/img/posts/mongodb_practice/24.jpg"></a>
  <a href="/assets/img/posts/mongodb_practice/25.jpg"><img src="/assets/img/posts/mongodb_practice/25.jpg"></a>
  <figcaption>Document</figcaption>
</figure>
