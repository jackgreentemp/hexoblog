---
title: Angular2 如何使用路由返回上一级，如何获取路由参数
date: 2016-10-18 19:17:16
tags: Angular2
---

# 参见官网教程
https://angular.cn/docs/ts/latest/tutorial/toh-pt5.html

#代码示例

```javascript
import { Component, OnInit }      from '@angular/core';
import { ActivatedRoute, Params } from '@angular/router';
import { Location }               from '@angular/common';

import { Hero }         from './hero';
import { HeroService }  from './hero.service';

@Component({
  moduleId: module.id,
  selector: 'my-hero-detail',
  templateUrl: 'hero-detail.component.html',
  styleUrls: [ 'hero-detail.component.css' ]
})
export class HeroDetailComponent implements OnInit {
  hero: Hero;

  constructor(
    private heroService: HeroService,
    private route: ActivatedRoute,
    private location: Location
  ) {}

  ngOnInit(): void {
    //获取路由参数
    this.route.params.forEach((params: Params) => {
      let id = +params['id'];
      this.heroService.getHero(id)
        .then(hero => this.hero = hero);
    });
  }

  //返回上一级
  goBack(): void {
    this.location.back();
  }
}
```
