<template>
    <el-container style="border: 1px solid #eee">
      <el-aside width="200px" style=" text-align: left;background-color: rgb(238, 241, 246)">
        <el-button :title="titles" @click="addNote" type="primary" icon="el-icon-circle-plus-outline">新增</el-button>
   
        <el-menu>
          <el-menu-item @click="selectNote(note)" v-bind:index="note.id"  v-bind:key='note.id' v-for='note of notes'>
          
            <span slot="title">{{ note.title }}</span>
           </el-menu-item>
       </el-menu>
      </el-aside>
      <template v-if="selectedNote">
      <el-main style="padding: 0px; width: 50%">
        <el-container>
          <el-main style="padding: 0 5px 10px 0;overflow:hidden;height:10%;width: 100%">
              <textarea style="margin: 0;height:100%; width: 99%" v-model="selectNote.content"></textarea>
          </el-main>
          <el-footer style="padding: 0 5px 10px 0;">
            footer
          </el-footer>
        </el-container>

      </el-main>
      
      <el-main style="padding: 0;width: 50%;text-align: left;" v-html="calc"></el-main>
      </template>
    </el-container>
  
</template>
<style lang="stylus">
body,
html
  height: 100%
  margin: 0px
.el-container
  height: 100%
.el-footer
  background-color: #B3C0D1;
  color: #333;
  text-align: left;
  line-height: 40px;
  margin: 0 5px 0px 0;
</style>
<script>
import marked from 'marked';
export default {
  name: 'HelloWorld',
  data () {
    return {
      notes: [],
      selectedId: null
    }
  },
  watch: {
    notes: 'saveNotes'
  },
  methods: {
    selectNote (note) {
      // console.log(arguments[0], arguments[1])
      this.selectedId = note.id
    },
    addNote () {
      const time = Date.now()
      const note = {
        id: String(time),
        title: '新note ' + (this.notes.length + 1),
        content: '**hi** this notebook is using [markdown](https://www.baidu.comm)',
        created: time,
        favorite: false
      }
      this.notes.push(note)
    },
    saveNotes(){
      localStorage.setItem('notes', JSON.stringify(this.notes))
      console.log('Notes saved!', new Date())
    }
  },
  props: {
    msg: String
  },
  computed: {
    selectedNote(){
      //返回与selectedID 匹配的笔记 
      return this.notes.find(note => note.id === this.selectedId)
    },
    titles () {
      return '共有:' + this.notes.length + '条note'
    },
    calc () {
      return this.selectedNote ?  marked(this.selectedNote.content) : ''
    }
  }
}
</script>
