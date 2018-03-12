input 图标
图标也是通过具名slot传入的，也可以通过prop中的icon传入图标名。

<slot name="icon">
  <i
    class="el-input__icon"
    :class="'el-icon-' + icon"
    v-if="icon"
    @click="handleIconClick">
  </i>
</slot>
上面还绑定了一个handleIconClick的点击事件，它会触发click事件：

methods: {
  handleIconClick(event) {
    this.$emit('click', event);
  },
}
input
然后是最重要的input部分，上面大部分是prop，不进行讲解，其余的我们将一一讲解。

<input
  v-if="type !== 'textarea'"
  class="el-input__inner"
  :type="type"  // 类型
  :name="name"  // 名字
  :placeholder="placeholder"  // 默认值
  :disabled="disabled"  // 是否禁用
  :readonly="readonly"  // 是否只读
  :maxlength="maxlength"  // 输入的最大长度
  :minlength="minlength"  // 输入的最小长度（暂时不支持）
  :autocomplete="autoComplete"  // 自动补全
  :autofocus="autofocus"  // 自动聚焦
  :min="min"  // 允许输入的最小值（数字或者日期）
  :max="max"  // 允许输入的最大值（数字或者日期）
  :form="form"  // 绑定的表单（不是原生的）
  :value="currentValue"  // 输入值
  ref="input"  // 引用
  @input="handleInput"  // 输入事件
  @focus="handleFocus"  // 获得焦点事件
  @blur="handleBlur"  // 失去焦点事件
>
value
value改变的时候会调用setCurrentValue。

watch: {
  'value'(val, oldValue) {
    this.setCurrentValue(val);
  }
},
而setCurrentValue是用来改变当前值的。

methods: {
  setCurrentValue(value) {
    if (value === this.currentValue) return;  // 如果新旧值一致直接返回

    this.$nextTick(_ => {
      this.resizeTextarea();  // 下一个DOM更新周期时，重新设置文本域大小
    });

    this.currentValue = value;  // 改变当前值
    this.$emit('input', value);  // 触发 input 事件
    this.$emit('change', value);  // 触发 change 事件
    this.dispatch('ElFormItem', 'el.form.change', [value]);  // 向父级的派发 el.form.change 事件
  }
}
handleInput
处理输入事件。

methods: {
  handleInput(event) {
    this.setCurrentValue(event.target.value);  // 改变当前值
  },
}
handleFocus
handleFocus用来处理获得焦点的事件，会直接触发focus事件。

methods: {
  handleFocus(event) {
    this.$emit('focus', event);
  },
}
handleBlur
handleBlur用来处理失去焦点的事件。

methods: {
  handleBlur(event) {
    this.$emit('blur', event);  // 触发 blur 事件
    this.dispatch('ElFormItem', 'el.form.blur', [this.currentValue]);  // 向父组件派发 el.form.blur 事件
  },
}
loading
loading会根据计算属性validating来决定是否渲染。

computed: {
  validating() {
    return this.$parent.validateState === 'validating';
  }
},
<i class="el-input__icon el-icon-loading" v-if="validating"></i>
后置元素
后置元素只能根据具名slot传入。

<div class="el-input-group__append" v-if="$slots.append">
  <slot name="append"></slot>
</div>
Textarea
如果type设置为textarea则会渲染textarea，上面绑定的都和input类似，不再多说，多了一个textareaStyle，是根据calcTextareaHeight计算出来的。

<textarea
  v-else
  class="el-textarea__inner"
  :value="currentValue"
  @input="handleInput"
  ref="textarea"
  :name="name"
  :placeholder="placeholder"
  :disabled="disabled"
  :style="textareaStyle"
  :readonly="readonly"
  :rows="rows"
  :form="form"
  :autofocus="autofocus"
  :maxlength="maxlength"
  :minlength="minlength"
  @focus="handleFocus"
  @blur="handleBlur">
</textarea>
