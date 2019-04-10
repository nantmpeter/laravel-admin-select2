# Laravel-Admin select2 extension

中文用户请阅读 [中文文档](README_cn.md).

A asynchronous extension to implements select2 to [laravel-admin](http://github.com/z-song/laravel-admin/), including single select/multiple select/morph select.

It will send ajax query if only you trigger list option in the form.

*. extends from laravel-admin's original select, multipleSelect Fields, so it's compatible with laravel-admin select field.

[![travis.svg](https://img.shields.io/travis/xiaohuilam/laravel-admin-select2/master.svg?style=flat-square)](https://travis-ci.org/xiaohuilam/laravel-admin-select2)
[![styleci.svg](https://github.styleci.io/repos/178165826/shield?branch=master)](https://github.styleci.io/repos/178165826)
[![version.svg](https://img.shields.io/packagist/vpre/xiaohuilam/laravel-admin-select2.svg?style=flat-square)](https://packagist.org/packages/xiaohuilam/laravel-admin-select2)
[![issues-open.svg](https://img.shields.io/github/issues/xiaohuilam/laravel-admin-select2.svg?style=flat-square)](https://github.com/xiaohuilam/laravel-admin-select2/issues)
[![last-commit.svg](https://img.shields.io/github/last-commit/xiaohuilam/laravel-admin-select2.svg?style=flat-square)](https://github.com/xiaohuilam/laravel-admin-select2/commits/)
[![contributors.svg](https://img.shields.io/github/contributors/xiaohuilam/laravel-admin-select2.svg?style=flat-square)](https://github.com/xiaohuilam/laravel-admin-select2/graphs/contributors)
[![install-count.svg](https://img.shields.io/packagist/dt/xiaohuilam/laravel-admin-select2.svg?style=flat-square)](https://packagist.org/packages/xiaohuilam/laravel-admin-select2)
[![license.svg](https://img.shields.io/github/license/xiaohuilam/laravel-admin-select2.svg?style=flat-square)](LICENSE)

## Installation
```bash
composer require xiaohuilam/laravel-admin-select2
```

## Usage

**code**
```php
<?php

namespace App\Admin\Controllers;

use App\Http\Controllers\Controller;
use Encore\Admin\Form;
use App\Models\User;
use App\Models\Answer;
use App\Models\Comment;
use App\Models\UserResource;
use Illuminate\Support\Facades\DB;

class YourController extends Controller
{
    protected function form()
    {
        $form = new Form(new UserResource);

        //single select
        $form->select('user_id', 'User id')->match(function ($keyword) {
            /**
             * @var \Illuminate\Database\Eloquent\Builder $query Query object，**remeber if does not contains `text` and `id` columns, write sql to AS!**
             */
            $query = User::where('name', 'LIKE', '%' . $keyword . '%')->select([DB::raw('name AS text'), 'id',]);
            return $query;
        })->text(function ($id) {
            return User::where(app(User::class)->getKeyName(), $id)->pluck('name', 'id');
        });

        //multiple select
        $form->multipleSelect('tags', 'Tags')->match(
            function ($keyword) {
                return Tag::where('name', 'LIKE', '%' . $keyword . '%')->select([DB::raw('name AS text'), 'id',]);
            }
        )
        ->text(
            function ($id_list) {
                return Tag::whereIn(app(Tag::class)->getKeyName(), $id_list)->pluck('name', 'id');
            }
        );

        //morph select
        $form->morphSelect('commentable')->type([
            Comment::class => 'Comment',
            Answer::class => 'Answer',
        ]);

        $form->text('title', 'Title');
        $form->textarea('content', 'Content');

        return $form;
    }
}
```

**Screenshot**

![screenshot.png](https://wantu-kw0-asset007-hz.oss-cn-hangzhou.aliyuncs.com/G5l12nD7D73p56dvXBm.png?x-oss-process=image/resize,l_500)


## LICENSE

Open source under [MIT](LICENSE) LICENSE.