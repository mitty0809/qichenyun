<template>
  <div class="top-menu">
    <el-menu :default-active="activeIndex"
             mode="horizontal"
             text-color="#333">
      <template v-for="(item,index) in items">
        <el-menu-item :index="item.parentId+''"
                      @click.native="openMenu(item)"
                      :key="index">
          <template slot="title">
            <i :class="item.icon"></i>
            <span>{{generateTitle(item)}}</span>
          </template>
        </el-menu-item>
      </template>
    </el-menu>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
export default {
  name: "top-menu",
  data () {
    return {
      activeIndex: "0",
      items: [],
      listmenu:[]
    };
  },
  created () {
    // this.getMenu();
  //   var getTree=function(treeData,parentId){
  //      var treeArr=[];
  //      for(var i=0;i<treeData.length;i++){ 
  //          var node=treeData[i];
  //           if(node.parentId==parentId ){ 
  //              var newNode={id:node.id,title:node.name,children:getTree(treeData,node.id)};
  //              treeArr.push(newNode);              
  //           }
  //      }
  //      return treeArr;
  //  }
  },
  computed: {
    ...mapGetters(["tagCurrent", "menu"])
  },
  methods: {
    getMenu () {
      this.$store.dispatch("GetTopMenu").then(res => {
        this.items = res;
      });
    },
    generateTitle (item) {
      return this.$router.$avueRouter.generateTitle(
        item.label,
        (item.meta || {}).i18n
      );
    },
    openMenu (item) {
      this.$store.dispatch("GetMenu", item.parentId).then(data => {
        if (data.length !== 0) {
          // this.listmenu=getTree(data,'')
          // console.log(this.listmenu)
          // console.log(data)
          this.$router.$avueRouter.formatRoutes(data, true);
        }
        let itemActive,
          childItemActive = 0;
        if (item.path) {
          itemActive = item;
        } else {
          if (this.menu[childItemActive].length == 0) {
            itemActive = this.menu[childItemActive];
          } else {
            itemActive = this.menu[childItemActive].children[childItemActive];
          }
        }
        this.$router.push({
          path: this.$router.$avueRouter.getPath({
            name: itemActive.label,
            src: itemActive.path
          }, itemActive.meta)
        });
      });
    }
  }
};
</script>