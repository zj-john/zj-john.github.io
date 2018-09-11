---
title: react-bootstrap-typeahead 设置不可选option
categories:
  - tips
tags:
  - react-bootstrap-typeahead
toc: false
date: 2018-03-26 10:33:49
---

react中使用了[react-bootstrap-typeahead](https://github.com/ericgio/react-bootstrap-typeahead)插件，需要在样式里做分组，类似于optgroup的样式。  
所以optgroup的label需要不可选择,在插件的说明文档中并没有这个说明。  
需要怎么配置呢？

<!-- more -->
找了下源码，看到源码中实际上做了这一部分，但是没有说明出来。  
render option的代码如下：
```js
// TypeaheadMenu.react.js
const menuItemProps = {
  // 有disabled的props
  disabled: option.disabled,
  key: idx,
  label: getOptionLabel(option, labelKey),
  option,
  position: idx,
};

if (option.customOption) {
  return (
    <MenuItem
      {...menuItemProps}
      className="rbt-menu-custom-option"
      label={newSelectionPrefix + option[labelKey]}>
      {newSelectionPrefix}
      <Highlighter search={text}>
        {option[labelKey]}
      </Highlighter>
    </MenuItem>
  );
}

return (
  <MenuItem {...menuItemProps}>
    {renderMenuItemChildren(option, this.props, idx)}
  </MenuItem>
);

// MenuItem.react.js
return (
    /* eslint-disable jsx-a11y/anchor-is-valid */
    <li
      {...props}
      className={cx(conditionalClassNames, className)}>
      <a
        className={cx('dropdown-item', conditionalClassNames)}
        href="#"
        onClick={this._handleClick}
        onMouseDown={onMouseDown}>
        {children}
      </a>
    </li>
    /* eslint-enable jsx-a11y/anchor-is-valid */
  );
}

_handleClick = (e) => {
  const {disabled, onClick} = this.props;

  e.preventDefault();
  // disabled为false时才触发onclick事件
  !disabled && onClick(e);
}
```

修改了代码后，已经无法点击optgroup的label，但是仍可以通过键盘Enter来选择optgroup。  

这个问题在[issue:304](https://github.com/ericgio/react-bootstrap-typeahead/issues/304)做了修复，发布在新版本v2.3.2中。我当前使用的版本1.4.2过低，升级一下再试试。
