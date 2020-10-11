<template>
  <div>
    <textarea
      v-model="arr"
      cols="50"
      rows="10"
      class="showtext"
      placeholder="请输入师生信息..."
    ></textarea>

    <vue2-org-tree
      :data="data"
      :horizontal="true"
      :render-content="renderContent"
      collapsable
      @on-expand="onExpand"
      @on-node-click="onNodeClick"
      class="treeplace"
    />

    <div class="tipBg" v-if="tipShow">
      <div class="tipBox">
        <div class="tip-title">添加下级</div>
        <input type="text" placeholder="请输入内容" v-model="inputValue" />
        <div class="div-button" @click="cancel">取消</div>
        <!-- <button @click="addNode">addNode</button> -->
        <div class="div-button" @click="addNode">增加</div>
      </div>
    </div>
    <button @click="callWindow">增加内容</button>
    <button @click="createTree" class="midbutton">生成</button>
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

      expandAll: false,

      tipShow: false,
      inputValue: null,
      nodeData: null,
    };
  },
  methods: {
    renderContent: function (h, data) {
      return data.label;
    },
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
    onNodeClick: function (e, data) {
      this.nodeData = data;
    },
    callWindow() {
      this.tipShow = !this.tipShow;
    },
    addNode() {
      this.tipShow = false;
      let childrenList;
      if (this.nodeData.children) {
        childrenList = this.nodeData.children.concat({
          label: this.inputValue,
        });
      } else {
        childrenList = [{ label: this.inputValue }];
      }
      this.$set(this.nodeData, "children", childrenList);
      this.$set(this.nodeData, "expand", true);
      //this.cancel();
    },

    cancel() {
      this.tipShow = false;
      this.inputValue = null;
    },
    createTree() {
      this.data = { label: "", children: [] };
      var trees = this.arr.split("\n\n\n");
      trees.forEach((treeText) => {
        let lines = treeText.split("\n");
        if (lines[0].slice(0, 2) !== "导师") {
          alert("输入错误请重新输入");
          return;
        }
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
            var flag2 = false;
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

                if (name === this.data.children[k].children[m].label) {
                  var skill = {
                    label: lines[j].slice(loc + 1, lines[j].length),
                    children: [],
                  };
                  this.data.children[k].children[m].children.push(skill);
                  flag2 = true;
                }
              }
            }
            if (!flag2) {
              alert('输入学生信息有误，"' + name + '"不在师门树中');
            }
            flag = false;
          }
        }
      });
    },
  },
};
</script>

<style scoped>
.showtext {
  width: 50%;
  height: 500px;
  resize: none;
  font-size: 20px;
  font-family: "Microsoft YaHei", 微软雅黑, "MicrosoftJhengHei", 华文细黑,
    STHeiti, MingLiu;
}
.midbutton {
  position: absolute;
  left: 46.5%;

  top: 530px;
  display: inline-block;
  zoom: 1;
  *display: inline;
  vertical-align: baseline;

  outline: none;
  cursor: pointer;
  text-align: center;
  text-decoration: none;
  font: 14px/100% Arial, Helvetica, sans-serif;
  padding: 0.5em 2em 0.55em;
  text-shadow: 0 1px 1px rgba(0, 0, 0, 0.3);
  -webkit-border-radius: 0.5em;
  -moz-border-radius: 0.5em;
  border-radius: 0.5em;
  -webkit-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
  -moz-box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
  font-size: 20px;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

.treeplace {
  position: absolute;
  top: 580px;
  left: 10%;
}
.tree-box {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: white;
}
.tipBg {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 9999;
}
.tipBox {
  width: 350px;
  height: 200px;
  border-radius: 10px 10px;
  background-color: white;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  overflow: hidden;
  margin: auto;
}
.tip-title {
  width: 100%;
  height: 40px;
  background-color:#e84393;
  color: white;
  text-align: center;
  line-height: 40px;
  letter-spacing: 2px;
}
.tipBox input {
  width: 80%;
  height: 40px;
  margin: 30px 10%;
  border: solid 1px black;
  border-radius: 5px 5px;
  padding-left: 20px;
  box-sizing: border-box;
}
.tipBox .div-button {
  width: 80px;
  height: 30px;
  text-align: center;
  line-height: 30px;
  background-color:#e84393;
  color: black;
  cursor: pointer;
  border-radius: 5px;
  display: inline-block;
  margin-left: 65px;
}
</style>
