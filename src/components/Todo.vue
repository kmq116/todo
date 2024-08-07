<template>
  <a-layout style="min-height: 100vh">
    <a-layout-content style="padding: 50px">
      <a-card title="Todo" style=" margin: 0 auto">
        <a-input-search
            v-model:value="newTodo"
            placeholder="添加新的Todo项"
            enter-button="添加"
            @search="addTodo"
        />
        <template v-for="(todosForDate, date) in groupedTodos" :key="date">
          <a-collapse style="margin-top: 20px" :active-key="expandedDates.includes(date) ? [date] : []">
            <a-collapse-panel :header="date" @click.stop="toggleExpand(date)" :key="date">
              <a-list
                  :dataSource="todosForDate"
                  :locale="{ emptyText: '该日期暂无Todo项' }"
              >
                <template #renderItem="{ item }">
                  <a-list-item @click.stop>
                    <template #actions>
                      <a-button type="link" @click="removeTodo(item)">删除</a-button>
                    </template>
                    <a-checkbox v-model:checked="item.completed">
                      <span :class="{ completed: item.completed }">{{ item.text }}</span>
                    </a-checkbox>
                  </a-list-item>
                </template>
              </a-list>
            </a-collapse-panel>
          </a-collapse>
        </template>
      </a-card>
    </a-layout-content>
  </a-layout>
</template>

<script setup>
import {computed, ref, watch} from 'vue'
import {useLocalStorage} from '@vueuse/core'
import {message} from 'ant-design-vue'
import {v4 as uuidv4} from 'uuid';


const todos = useLocalStorage('todos', {})
const newTodo = ref('')
const groupedTodos = ref([])

watch(todos, () => {

  const grouped = {}

  Object.values(todos.value).forEach(todo => {
    const date = new Date(todo.createTimeStamp).toLocaleDateString()
    if (!grouped[date]) {
      grouped[date] = []
    }
    grouped[date].push(todo)
  })
  console.log(Object.values(grouped).length)
  console.log(Object.values(grouped).sort(), '---')
  Object.values(grouped).forEach(ls => {
    ls.sort((a, b) => {
          console.log(a, b)
          if (!a.completed) return -1
          else {
            console.log(a.createTimeStamp - b.createTimeStamp)
            return a.createTimeStamp - b.createTimeStamp
          }
        }
    )
  })


  groupedTodos.value = grouped
}, {
  deep: true,
  immediate: true
})
// Function to sort todos based on their creation timestamp

const expandedDates = ref([])

// Watcher to update grouped todos whenever the todos change
// const groupedTodos = computed(() => {
//
// })

const addTodo = () => {
  if (newTodo.value.trim()) {
    const id = uuidv4()
    todos.value[id] = {
      id: id,
      text: newTodo.value,
      completed: false,
      createTimeStamp: Date.now()
    }
    newTodo.value = ''
    message.success('Todo项添加成功')
  } else {
    message.warning('请输入有效的Todo项')
  }
}

const removeTodo = (item) => {
  console.log(item)
  delete todos.value[item.id]
  message.success('Todo项删除成功')
}

const curIndex = ref(0)
const toggleExpand = (date) => {
  console.log('data', date)
  const index = expandedDates.value.indexOf(date)
  // if (curIndex.value === index) return
  if (index === -1) {
    expandedDates.value.push(date)
  } else {
    expandedDates.value.splice(index, 1)
    curIndex.value = index
  }

}
</script>

<style scoped>
.completed {
  text-decoration: line-through;
  color: #999;
}
</style>
