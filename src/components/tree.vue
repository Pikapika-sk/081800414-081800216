<template>
  <div>
    <textarea v-model="arr" cols="30" rows="10"></textarea>
    <vue2-org-tree
      :data="data"
      :horizontal="true"
      collapsable
      @on-expand="onExpand"
    />
    <button @click="coutArr">生成</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      arr: "",
      data: {
        label: "",
        children: [],
      },
      collapsable: true,
    };
  },
  methods: {
    collapse(list) {
      var _this = this;
      list.forEach(function (child) {
        if (child.expand) {
          child.expand = false;
        }
        child.children && _this.collapse(child.children);
      });
    },
    onExpand(e, data) {
      if ("expand" in data) {
        data.expand = !data.expand;
        if (!data.expand && data.children) {
          this.collapse(data.children);
        }
      } else {
        this.$set(data, "expand", true);
      }
    },
    createFather() {
      this.data.label = "张三";
    },

    coutArr() {
      this.data = { label: "", children: [] };
      var trees = this.arr.split("\n\n\n");
      trees.forEach((treeText) => {
        let lines = treeText.split("\n");
        if (lines[0].slice(0, 2) !== "导师") alert("输入错误请重新输入");
        this.data.label = lines[0].slice(3, lines[0].length);
        var flag = false;
        for (var j = 1; j < lines.length; ++j) {
          if (lines[j] === "") {
            flag = true;
            //console.log(flag);
            continue;
          }
          if (!flag) {
            var loc = lines[j].indexOf("：");
            var it = {
              label: lines[j].slice(0, loc),
              children: [],
            };
            var n = 0;
            var last = loc;
            for (var i = loc; i < lines[j].length; ++i) {
              if (lines[j][i] == "、" || i == lines[j].length - 1) {
                var str2;
                if (i == lines[j].length - 1)
                  str2 = lines[j].slice(last + 1, i + 1);
                else str2 = lines[j].slice(last + 1, i);
                var it2 = {
                  label: str2,
                  children: [],
                };
                it.children[n++] = it2;
                last = i;
              }
            }
            // console.log(lines[j]);
            this.data.children.push(it);
          } else {
            //  console.log(lines[j]);
            loc = lines[j].indexOf("：");
            var name = lines[j].slice(0, loc);
            name = String(name);
            // console.log(name);
            for (var k = 0; k < this.data.children.length; ++k) {
              for (var m = 0; m < this.data.children[k].children.length; ++m) {
                //console.log(this.data.children[k].children[m].label);
                this.data.children[k].children[m].label = String(
                  this.data.children[k].children[m].label
                );

                if (name === this.data.children[k].children[m].label)
                {
                  var skill = {
                    label:lines[j].slice(loc+1,lines[j].length),
                    children:[]
                  };
                  this.data.children[k].children[m].children.push(skill);
                }
              }
            }
            flag = false;
          }
        }
      });
    },
  },
};
</script>

<style>
</style>