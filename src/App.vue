<template>
  <transition name="fade">
    <router-view></router-view>
  </transition>
</template>

<script>
export default {};
</script>

<style>
.el-button--primary {
  background-color: #8a1913;
  border-color: #8a1913;
}
.el-button--primary:focus, .el-button--primary:hover{
  background-color: #b48200;
  border-color: #b48200;
}
.el-button--text:active{
  color:#8a1913;
}
.el-button--text, .el-button--text:focus, .el-button--text:hover{
  color:#8a1913;
}
.el-button:focus, .el-button:hover{
  background-color: transparent;
  color:#8a1913;
  border:1px solid #8a1913;
}
.cell > .el-button, .cell > .el-button:focus, .cell > .el-button:hover{
  border:0px transparent;
}
.el-table--striped .el-table__body tr.el-table__row--striped.current-row td, .el-table__body tr.current-row>td, .el-table__body tr.hover-row.current-row>td, .el-table__body tr.hover-row.el-table__row--striped.current-row>td, .el-table__body tr.hover-row.el-table__row--striped>td, .el-table__body tr.hover-row>td{
  background-color: #f3f3f3;
}
.el-message-box__status.el-icon-warning{
  color:#8a1913;
}
.el-message-box__headerbtn:focus .el-message-box__close, .el-message-box__headerbtn:hover .el-message-box__close{
  color:#8a1913;
}
.el-tabs__item:hover, .el-tabs__item.is-active{
  color:#8a1913;
}
.el-tabs__active-bar{
  background-color:#8a1913;
}
.el-checkbox__input.is-checked .el-checkbox__inner, .el-checkbox__input.is-indeterminate .el-checkbox__inner{
  background-color:#8a1913;
  border-color:#8a1913;
}
.el-checkbox__inner:hover{
  border-color:#8a1913;
}
.el-pager li.active{
  color:#8a1913;
}
.el-select .el-input.is-focus .el-input__inner{
  border-color:#8a1913;
}
.el-select-dropdown__item.selected{
  color:#8a1913;
}
.el-select .el-input__inner:focus, .el-pagination__sizes .el-input .el-input__inner:hover, .el-input.is-active .el-input__inner, .el-input__inner:focus, .el-textarea__inner:focus{
  border-color:#8a1913;
}
.el-button--danger{
  background-color:#A67A2B;
  border-color:#A67A2B;
}
.el-button--danger.is-disabled, .el-button--danger.is-disabled:active, .el-button--danger.is-disabled:focus, .el-button--danger.is-disabled:hover{
  background-color:#D6AC69;
  border-color:#D6AC69;
}
.el-loading-spinner .el-loading-text{
  color:#8a1913;
}
.el-slider__bar{
  background-color:#8a1913;
}
.el-slider__button{
  border-color:#8a1913;
}
.el-rate{
  margin-bottom:20px;
}
.el-select-dropdown.is-multiple .el-select-dropdown__item.selected{
  color:#A67A2B;
}
.el-loading-spinner .path{
  stroke: #8a1913;
}
@media screen and (max-width: 600px) {
  .el-menu-item {
    padding: 0 10px;
  }
}
</style>