---
name: angular-coding
description: "综合Angular编码技能，涵盖组件开发、状态管理、路由配置、API服务、测试和质量保证、构建部署等核心领域，用于解决Angular开发中的各种编码问题。"
license: Apache 2.0
---

# Angular编码综合技能

## 概述

本技能提供Angular开发的综合指南，涵盖组件开发、状态管理、路由配置、API服务、测试和质量保证、构建部署等核心领域，帮助前端开发人员解决Angular编码中的各种问题，提高开发效率和代码质量。

## 核心内容

### 1. 组件开发

#### 基本组件结构（使用信号）
```typescript
import { Component, input, output } from '@angular/core';

@Component({
  selector: 'app-user-profile',
  template: `
    <div class="user-profile">
      <h2>{{ user().name }}</h2>
      <p>{{ user().email }}</p>
      <button (click)="onContact.emit(user().id)">Contact</button>
    </div>
  `,
  styles: [`
    .user-profile { padding: 16px; border: 1px solid #e0e0e0; border-radius: 8px; }
  `]
})
export class UserProfileComponent {
  user = input.required<{ id: string; name: string; email: string }>();
  onContact = output<string>();
}
```

#### 表单处理
```typescript
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-user-form',
  template: `
    <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
      <div>
        <label for="name">Name:</label>
        <input type="text" id="name" formControlName="name">
        <div *ngIf="userForm.controls.name?.invalid && userForm.controls.name?.touched">
          Name is required
        </div>
      </div>
      <button type="submit" [disabled]="userForm.invalid">Submit</button>
    </form>
  `
})
export class UserFormComponent {
  userForm: FormGroup;

  constructor(private fb: FormBuilder) {
    this.userForm = this.fb.group({
      name: ['', Validators.required],
      email: ['', [Validators.required, Validators.email]]
    });
  }

  onSubmit() {
    if (this.userForm.valid) {
      console.log('Form submitted:', this.userForm.value);
    }
  }
}
```

#### 使用model()进行双向绑定
```typescript
import { Component, model } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <div class="counter">
      <button (click)="decrement()">-</button>
      <span>{{ count() }}</span>
      <button (click)="increment()">+</button>
    </div>
  `,
  styles: [
    .counter { display: flex; align-items: center; gap: 16px; }
  ]
})
export class CounterComponent {
  count = model(0);

  increment() {
    this.count.update(current => current + 1);
  }

  decrement() {
    this.count.update(current => Math.max(0, current - 1));
  }
}

// 使用示例
// <app-counter [(count)]="currentCount"></app-counter>

#### 基于Formly的动态表单
```typescript
import { Component, signal } from '@angular/core';
import { FormGroup } from '@angular/forms';
import { FormlyFormOptions, FormlyFieldConfig } from '@ngx-formly/core';
import { FormlyNzFormModule } from '@ngx-formly/ng-zorro-antd';

@Component({
  selector: 'app-dynamic-form',
  template: `
    <div class="dynamic-form">
      <h2>动态表单</h2>
      <form [formGroup]="form" (ngSubmit)="onSubmit()">
        <formly-form
          [form]="form"
          [fields]="fields()"
          [model]="model()"
          [options]="options"
        ></formly-form>
        <button type="submit" nz-button nzType="primary">提交</button>
      </form>
      <div *ngIf="submitted()" class="form-result">
        <h3>表单结果</h3>
        <pre>{{ model() | json }}</pre>
      </div>
    </div>
  `,
  styles: [
    .dynamic-form { padding: 16px; border: 1px solid #e0e0e0; border-radius: 8px; }
    .form-result { margin-top: 16px; padding: 16px; background: #f5f5f5; border-radius: 8px; }
  ]
})
export class DynamicFormComponent {
  form = new FormGroup({});
  model = signal({
    name: '',
    email: '',
    age: null,
    gender: '',
    address: {
      street: '',
      city: '',
      zipCode: ''
    }
  });
  options: FormlyFormOptions = {};
  fields = signal<FormlyFieldConfig[]>([
    {
      key: 'name',
      type: 'input',
      props: {
        label: '姓名',
        placeholder: '请输入姓名',
        required: true,
      },
    },
    {
      key: 'email',
      type: 'input',
      props: {
        label: '邮箱',
        placeholder: '请输入邮箱',
        required: true,
        type: 'email',
      },
      validators: {
        validation: ['email'],
      },
    },
    {
      key: 'age',
      type: 'input',
      props: {
        label: '年龄',
        placeholder: '请输入年龄',
        type: 'number',
        min: 18,
        max: 100,
      },
    },
    {
      key: 'gender',
      type: 'select',
      props: {
        label: '性别',
        placeholder: '请选择性别',
        options: [
          { label: '男', value: 'male' },
          { label: '女', value: 'female' },
        ],
      },
    },
    {
      key: 'address',
      type: 'fieldset',
      props: {
        label: '地址',
      },
      fieldGroup: [
        {
          key: 'street',
          type: 'input',
          props: {
            label: '街道',
            placeholder: '请输入街道',
          },
        },
        {
          key: 'city',
          type: 'input',
          props: {
            label: '城市',
            placeholder: '请输入城市',
          },
        },
        {
          key: 'zipCode',
          type: 'input',
          props: {
            label: '邮编',
            placeholder: '请输入邮编',
          },
        },
      ],
    },
  ]);
  submitted = signal(false);

  onSubmit() {
    if (this.form.valid) {
      this.submitted.set(true);
      console.log('Form submitted:', this.model());
    }
  }
}
```

### 2. 状态管理和路由

#### 路由配置
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { 
    path: 'admin', 
    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule) 
  },
  { 
    path: 'user/:id', 
    component: UserComponent,
    canActivate: [AuthGuard],
    resolve: { user: UserResolver }
  },
  { path: '404', component: NotFoundComponent },
  { path: '**', redirectTo: '/404' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { 
    preloadingStrategy: PreloadAllModules,
    scrollPositionRestoration: 'enabled' 
  })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

#### 信号（Signals）状态管理
```typescript
import { Component, signal, computed, effect } from '@angular/core';

@Component({
  selector: 'app-todo-list',
  template: `
    <h2>Todo List</h2>
    <div>{{ completedCount() }} / {{ todos().length }} completed</div>
    <ul>
      <li *ngFor="let todo of todos()">
        <input type="checkbox" [(ngModel)]="todo.completed" (change)="updateTodo(todo)">
        <span [class.completed]="todo.completed">{{ todo.title }}</span>
      </li>
    </ul>
  `
})
export class TodoListComponent {
  todos = signal([
    { id: 1, title: 'Learn Angular', completed: true },
    { id: 2, title: 'Build app', completed: false }
  ]);
  
  completedCount = computed(() => 
    this.todos().filter(todo => todo.completed).length
  );
  
  constructor() {
    effect(() => {
      console.log(`Todo count changed: ${this.todos().length}`);
    });
  }
  
  updateTodo(todo: any) {
    this.todos.update(todos => 
      todos.map(t => t.id === todo.id ? todo : t)
    );
  }
}
```

### 3. API服务开发

#### 基本服务实现
```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class UserService {
  private apiUrl = 'https://api.example.com/users';

  constructor(private http: HttpClient) { }

  getUsers(): Observable<any[]> {
    return this.http.get<any[]>(this.apiUrl);
  }

  getUserById(id: string): Observable<any> {
    return this.http.get<any>(`${this.apiUrl}/${id}`);
  }

  createUser(user: any): Observable<any> {
    return this.http.post<any>(this.apiUrl, user);
  }
}
```

#### HTTP拦截器
```typescript
import { Injectable } from '@angular/core';
import { HttpEvent, HttpInterceptor, HttpHandler, HttpRequest } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const token = localStorage.getItem('authToken');
    if (token) {
      const cloned = req.clone({
        headers: req.headers.set('Authorization', 'Bearer ' + token)
      });
      return next.handle(cloned);
    }
    return next.handle(req);
  }
}
```

### 4. 测试和质量保证

#### 单元测试
```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { UserProfileComponent } from './user-profile.component';

describe('UserProfileComponent', () => {
  let component: UserProfileComponent;
  let fixture: ComponentFixture<UserProfileComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [ UserProfileComponent ]
    }).compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(UserProfileComponent);
    component = fixture.componentInstance;
    component.user.set({ id: '1', name: 'John', email: 'john@example.com' });
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should emit event when button is clicked', () => {
    spyOn(component.onContact, 'emit');
    const button = fixture.nativeElement.querySelector('button');
    button.click();
    expect(component.onContact.emit).toHaveBeenCalledWith('1');
  });
});
```

#### ESLint配置
```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:@angular-eslint/recommended",
    "plugin:prettier/recommended"
  ],
  "rules": {
    "@angular-eslint/component-selector": [
      "error", { "type": "element", "prefix": "app", "style": "kebab-case" }
    ]
  }
}
```

### 5. 构建和部署

#### 构建命令
```bash
# 开发构建
ng build

# 生产构建
ng build --configuration production

# 构建并分析
ng build --configuration production --stats-json
npx webpack-bundle-analyzer dist/my-app/stats.json
```

#### GitHub Actions CI配置
```yaml
name: Angular CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-node@v3
      with: { node-version: 18.x, cache: 'npm' }
    - run: npm ci
    - run: npm run build -- --configuration production
    - run: npm test -- --watch=false --browsers=ChromeHeadless
```

## 最佳实践

### 组件开发
- 使用OnPush变更检测策略提高性能
- 组件应该保持小巧和单一职责
- **使用基于信号的输入/输出**：优先使用`input()`、`output()`和`model()`函数进行组件通信
- 避免在模板中使用复杂表达式
- 为组件编写单元测试
- **动态表单使用Formly**：复杂表单推荐使用@ngx-formly/core和@ngx-formly/ng-zorro-antd，提高表单开发效率和可维护性
- **Formly字段配置最佳实践**：
  - 将字段配置封装为可复用的函数或服务
  - 使用类型安全的字段配置
  - 合理使用字段组和嵌套字段
  - 为复杂表单实现懒加载字段配置

### 状态管理
- 简单状态使用Signals，复杂状态使用NgRx
- 保持状态不可变
- 避免全局状态膨胀
- 为状态管理编写测试

### API服务
- 使用拦截器处理通用逻辑
- 实现适当的缓存策略
- 使用类型安全的API响应
- 实现全局错误处理

### 路由
- 使用懒加载减少初始包大小
- 实现路由守卫保护敏感路由
- 使用解析器预加载数据
- 配置适当的404页面

### 性能优化
- 启用树摇和代码分割
- 优化大型列表（虚拟滚动、分页）
- 使用CDN分发静态资源
- 监控应用性能指标

### 基于信号的组件通信最佳实践
- 使用`input.required()`表示必填输入
- 使用`input()`带默认值表示可选输入
- 使用`output()`发送事件
- 使用`model()`实现双向绑定
- 在模板中使用`signal()`时需要调用（如`user().name`）
- 避免在模板中直接修改信号值
- 使用`update()`方法更新信号值，保持不可变性

## 常见问题解决方案

### 1. 组件不更新
**问题**：组件数据更新但视图不刷新
**解决方案**：
- 检查变更检测策略
- 确保使用不可变数据更新
- 手动触发变更检测（如果需要）
- 检查是否正确使用了Input/Output

### 2. 路由无法导航
**问题**：点击链接或调用router.navigate()无效果
**解决方案**：
- 检查路由配置
- 检查路由守卫
- 检查是否使用了正确的路由路径
- 检查是否正确导入了RouterModule

### 3. API请求失败
**问题**：HTTP请求返回错误
**解决方案**：
- 检查API端点URL
- 检查请求头配置
- 检查CORS配置
- 检查网络连接
- 实现适当的错误处理

### 4. 构建失败
**问题**：ng build命令失败
**解决方案**：
- 检查TypeScript错误
- 检查依赖项版本
- 检查angular.json配置
- 清理node_modules并重新安装

## 依赖项

- **@angular/core**: Angular核心库
- **@angular/common/http**: HTTP客户端
- **@angular/forms**: 表单处理
- **@angular/router**: 路由管理
- **@ngx-formly/core**: 动态表单核心库
- **@ngx-formly/ng-zorro-antd**: NG-ZORRO UI组件的Formly集成
- **@ngrx/store**: 状态管理（可选）
- **jasmine-core/karma**: 单元测试
- **cypress**: 端到端测试
- **eslint/prettier**: 代码质量

## 总结

本技能提供了Angular开发的综合指南，涵盖了从组件开发到构建部署的各个方面。通过遵循本技能中的最佳实践和代码示例，您可以解决Angular编码中的各种问题，创建高质量、可维护的Angular应用。