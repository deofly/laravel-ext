# Introduction

1. [Ali Cloud OSS](https://aliyun.com/product/oss): 借鉴了一些优秀的代码，综合各方，同时做了更多优化，将会添加更多完善的接口和插件，打造Laravel最好的OSS Storage扩展.

2. [Tencent Cloud COS](https://cloud.tencent.com/product/cos): 为Laravel提供COS存储支持.

## Require
- Laravel 5+
- cURL extension

## Installation

```shell
$ composer require "deofly/laravel-ext" -vvv
```
Then in your `config/app.php` add this line to providers array:
```php
Jacobcyl\AliOSS\AliOssServiceProvider::class,
Jacobcyl\AliOSS\AliOssServiceProvider::class
```

## Configuration for OSS
Add the following in app/filesystems.php:
```php
'disks'=>[
    ...
    'oss' => [
            'driver'        => 'oss',
            'access_id'     => '<Your Aliyun OSS AccessKeyId>',
            'access_key'    => '<Your Aliyun OSS AccessKeySecret>',
            'bucket'        => '<OSS bucket name>',
            'endpoint'      => '<the endpoint of OSS, E.g: oss-cn-hangzhou.aliyuncs.com | custom domain, E.g:img.abc.com>', // OSS 外网节点或自定义外部域名
            //'endpoint_internal' => '<internal endpoint [OSS内网节点] 如：oss-cn-shenzhen-internal.aliyuncs.com>', // v2.0.4 新增配置属性，如果为空，则默认使用 endpoint 配置(由于内网上传有点小问题未解决，请大家暂时不要使用内网节点上传，正在与阿里技术沟通中)
            'cdnDomain'     => '<CDN domain, cdn域名>', // 如果isCName为true, getUrl会判断cdnDomain是否设定来决定返回的url，如果cdnDomain未设置，则使用endpoint来生成url，否则使用cdn
            'ssl'           => <true|false> // true to use 'https://' and false to use 'http://'. default is false,
            'isCName'       => <true|false> // 是否使用自定义域名,true: 则Storage.url()会使用自定义的cdn或域名生成文件url， false: 则使用外部节点生成url
            'debug'         => <true|false>
    ],
    ...
]
```
Then set the default driver in app/filesystems.php:
```php
'default' => 'oss',
```
Ok, well! You are finish to configure. Just feel free to use Aliyun OSS like Storage!

## Usage
See [Larave doc for Storage](https://laravel.com/docs/5.2/filesystem#custom-filesystems)
Or you can learn here:

> First you must use Storage facade

```php
use Illuminate\Support\Facades\Storage;
```
> Then You can use all APIs of laravel Storage

```php
Storage::disk('oss'); // if default filesystems driver is oss, you can skip this step

//fetch all files of specified bucket(see upond configuration)
Storage::files($directory);
Storage::allFiles($directory);

Storage::put('path/to/file/file.jpg', $contents); //first parameter is the target file path, second paramter is file content
Storage::putFile('path/to/file/file.jpg', 'local/path/to/local_file.jpg'); // upload file from local path

Storage::get('path/to/file/file.jpg'); // get the file object by path
Storage::exists('path/to/file/file.jpg'); // determine if a given file exists on the storage(OSS)
Storage::size('path/to/file/file.jpg'); // get the file size (Byte)
Storage::lastModified('path/to/file/file.jpg'); // get date of last modification

Storage::directories($directory); // Get all of the directories within a given directory
Storage::allDirectories($directory); // Get all (recursive) of the directories within a given directory

Storage::copy('old/file1.jpg', 'new/file1.jpg');
Storage::move('old/file1.jpg', 'new/file1.jpg');
Storage::rename('path/to/file1.jpg', 'path/to/file2.jpg');

Storage::prepend('file.log', 'Prepended Text'); // Prepend to a file.
Storage::append('file.log', 'Appended Text'); // Append to a file.

Storage::delete('file.jpg');
Storage::delete(['file1.jpg', 'file2.jpg']);

Storage::makeDirectory($directory); // Create a directory.
Storage::deleteDirectory($directory); // Recursively delete a directory.It will delete all files within a given directory, SO Use with caution please.

// upgrade logs
// new plugin for v2.0 version
Storage::putRemoteFile('target/path/to/file/jacob.jpg', 'http://example.com/jacob.jpg'); //upload remote file to storage by remote url
// new function for v2.0.1 version
Storage::url('path/to/img.jpg') // get the file url
```

More development detail see [Aliyun OSS DOC](https://help.aliyun.com/document_detail/32099.html?spm=5176.doc31981.6.335.eqQ9dM)


## Configuration for COS

1. Add a new disk to your `config/filesystems.php` config:
 ```php
 <?php

 return [
    'disks' => [
        //...
        'cos' => [
         'driver' => 'cos',
        'region'          => env('COS_REGION', 'ap-guangzhou'),
        'credentials'     => [
            'appId'     => env('COS_APP_ID'),
            'secretId'  => env('COS_SECRET_ID'),
            'secretKey' => env('COS_SECRET_KEY'),
        ],
        'timeout'         => env('COS_TIMEOUT', 60),
        'connect_timeout' => env('COS_CONNECT_TIMEOUT', 60),
        'bucket'          => env('COS_BUCKET'),
        'cdn'             => env('COS_CDN'),
        'scheme'          => env('COS_SCHEME', 'https'),
        'read_from_cdn'   => env('COS_READ_FROM_CDN', false),
        ],
        //...
     ]
 ];
 ```

# Usage

```php
$disk = Storage::disk('cos');

// create a file
$disk->put('avatars/filename.jpg', $fileContents);

// check if a file exists
$exists = $disk->has('file.jpg');

// get timestamp
$time = $disk->lastModified('file1.jpg');
$time = $disk->getTimestamp('file1.jpg');

// copy a file
$disk->copy('old/file1.jpg', 'new/file1.jpg');

// move a file
$disk->move('old/file1.jpg', 'new/file1.jpg');

// get file contents
$contents = $disk->read('folder/my_file.txt');

// fetch url content
$file = $disk->fetch('folder/save_as.txt', $fromUrl);

// get file url
$url = $disk->getUrl('folder/my_file.txt');
```

[Full API documentation.](http://flysystem.thephpleague.com/api/)


# License

MIT