# Design Best Practices

It's not a strict do'es and don'ts which you must follow while designing, it's just a list of few things that you should keep in mind while designing webites.

## Contents

[Avoid Deep Nesting](#avoid-deep-nesting)

[Variable Naming Convention](#variable-naming-convention)

[Use @mixins](#use-mixins)

[Name spacing](#name-spacing)

[Don't mirror DOM structure](#dont-mirror-dom-structure)

[Never use ID selectors](#never-use-id-selectors)

[Not everything is !important](#not-everything-is-!important)

[Order matters](#order-matters)

[Component based styling](#component-based-styling)

[Other good practices](#other-good-practices)

### **Avoid Deep Nesting**

Avoid deep nesting you should try not to go beyond three level of nesting in SCSS.

Bad:

```scss
.parent-class {
  .child-class {
    .child-child-class {
      .target-class {
        background-color :red;
      }
    }
  }
}
```

Good:

```scss
.parent-class {
  .target-class {
    background-color :red;
  }
}
```

[🔝 Back to contents](#contents)

### **Variable Naming Convention**

One of the key feature of SCSS is to give us the chance to declare global variables, and as a designer you should grab this by both hands. Never declare variable based on the behavior or on their appearancs, declare it on the basis of purpose.

Bad:

```scss
$red-color : FF0000;
```

Good:

```scss
$primary-color: FF0000;
```
OR
```scss
$theme-color: FF0000;
```

[🔝 Back to contents](#contents)

### **Use @mixins**

Another good feature of SCSS is it gives liberty to create functions. When you know that you have a bunch of CSS and that needs to be reused with different values you should use @mixin such cases. For instance: when you are create class with different font-size for your project.

Bad:

```scss
.text-small {
  font-size:12px;
}
.text-medium {
  font-size:14px;
}
.text-large {
  font-size:16px;
}
```

Good:

```scss
// variable.scss

$typography: (
        small: 12px,
        medium: 14px,
        regular: 16px,
);

//mixin.scss

@mixin typography-class-generator() {
  @each $name, $value in $typography {
    &_#{$name} {
      font-size: $value !important;
    }
  }
}

// typography.scss

.text {
    @include typography-class-generator()
}
```

[🔝 Back to contents](#contents)

### **Name spacing**

Name spacing is a good thing but it should be done at component level not at project/app level.

Bad:

```scss
.nav,.home-nav,.profile-nav
```

Good:

```scss
.nav,.nav-bar,.nav-list
```

[🔝 Back to contents](#contents)


### **Don't mirror DOM structure**

Don't try to dig your SCSS to the level your DOM does, you know why? For two reasons,
1. SCSS is a great tool for code reusability and you are murdering it if you are replicating DOM structure.
2. You are also killing any possibility of flexible changes in the HTML by mirroring the DOM in SCSS and that's an offense!

[🔝 Back to contents](#contents) 

### **Never use ID selectors**

ID's are subjected to be unique, how can you use a unique selector for styling. You are killing the scope of reusability again a criminal offense!

[🔝 Back to contents](#contents) 


### **Wrap your custom style for library classes into your custom class**

When you wish to overright the thing that your design library provides it is a good move to wrap your custom style with a custom class. Consider you want to change the navbar color of Bootstrap Navbar.


Bad:

```scss
.navbar-nav {
  background-color:red;
}
```

Good:

```php
.app-navbar {
 .navbar-nav {
   background-color:red;
 }
}
```

[🔝 Back to contents](#contents)

### **Not everything is !important**

Avoid the use of ```!important``` until and unless it is really required. If you are using ```!important``` too many then it simple suggests that your code is not structured properly and it is messy. 

[🔝 Back to contents](#contents)

### **Order matters**

For the sake of consistency and readibility in code, developers should follow the order of rules.

1. Use ```@extend``` first. This let you know at first that this class inherits rules from elsewhere.
2. Use ```@include``` next. Having your mixins and functions included at top is nice to have, and also allows you to know what you will be overwriting (if needed).
3. Now you can write your regular CSS class or element rules.
4. Place nested pseudo classes and pseudo elements before any other element.
5. Finally, write other nested selectors like in the following example:

```scss
.homepage {
  @extend page;
  @include border-radius(5px);
    margin-left: 5px;
      &:after{
        content: ‘’;
      }
  a {
  
  }
  ul {
  }
}
```

**Note:** The order of rules depends on what you are using if don't have ```@extend``` then you can just begin with ```@include``` if you don't have as well ```@include``` then you can begin with other css rule and so on.

Another important thing to note here, is about importing. Import vendor and global dependencies first, then authored dependencies, then layouts, patterns, and finally the parts and blocks. This is important to avoid mixed imports and overwrite of rules, because the vendor and global rules can’t be managed by us.

[🔝 Back to contents](#contents)

### **Component based styling**

While writing CSS/SCSS prefer to use component based stylings rather than deploying all common styles into main file. Components are reusable entities which means they will have a common and same style throughout the app. This will much useful when you are working in frameworks like React.js, Vuejs, Angular.

There are two benefits of this approach:
1. This practice enhance code readability and reusability.
2. This avoids to create useless styles and avoid code redundency, much needed thing in current trend, since such things effects the loading time of any application.

[🔝 Back to contents](#contents)

### **Other good practices**

A thorough visit is mandatory to understand your app before implementing.

Break your wireframe into reusable components.

Prepare styleguide of reusable components and define the global scope things like variable, app colors etc.

[🔝 Back to contents](#contents)
