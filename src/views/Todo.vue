<template>
  <div class="todo px-6">
    <!-- Text field to input a new task -->
    <v-text-field outlined label="Thêm Công Việc" placeholder="Vd: Tuần này học xong khóa Vuetify" clearable hide-details v-model="newTaskTitle" @click:append="addTask" @keyup.enter="addTask" append-icon="mdi-plus-circle" class="mb-6"></v-text-field>

    <v-list flat class="pt-0">
      <draggable v-model="tasks" ghost-class="ghost" handle=".handle">
        <transition-group type="transition" name="flip-list">
          <!-- Looping for the tasks -->
          <div class="sortable" v-for="(task, i) in tasks" :key="task.id" @click="doneTask(task.id)">
            <v-list-item :class="{ 'blue lighten-5': task.done }">
              <template v-slot:default>
                <v-list-item-action>
                  <v-checkbox v-if="handle != true" :input-value="task.done"></v-checkbox>
                  <v-icon color="primary" class="handle" v-else>mdi-drag</v-icon>
                </v-list-item-action>

                <v-list-item-content>
                  <v-list-item-title :class="{ 'text-decoration-line-through': task.done }">{{ task.title }}</v-list-item-title>
                </v-list-item-content>

                <v-list-item-content v-if="task.dueDate" class="text-right text-uppercase">
                  <v-list-item-title class="caption">
                    <v-icon v-if="!task.expired || task.done" dense class="mr-1">mdi-calendar-outline</v-icon>
                    <v-icon v-else-if="!task.done && task.expired" dense class="mr-1 error--text">mdi-calendar-alert</v-icon>
                    <span :class="{ 'error--text font-weight-bold': task.expired && !task.done }"> {{ computedDue(task.dueDate) }}</span>
                  </v-list-item-title>
                </v-list-item-content>

                <v-menu>
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn dark icon v-bind="attrs" v-on="on">
                      <v-icon color="primary lighten-1">mdi-dots-vertical</v-icon>
                    </v-btn>
                  </template>

                  <v-list>
                    <v-list-item>
                      <v-list-item-title>
                        <div role="button" @click="(task.modal = true), (dialog = 0)">
                          <v-btn icon>
                            <v-icon color="grey">mdi-pencil</v-icon>
                          </v-btn>
                          Chỉnh Sửa
                        </div>
                      </v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-title>
                        <div role="button" @click="(task.modal = true), (dialog = 1)">
                          <v-btn icon>
                            <v-icon color="grey">mdi-calendar-clock</v-icon>
                          </v-btn>
                          Hẹn Ngày
                        </div>
                      </v-list-item-title>
                    </v-list-item>
                    <v-list-item v-for="(item, i) in sideMenu" :key="i">
                      <v-list-item-title role="button" v-if="(item.title = 'Xóa')" @click.stop="handleFnCall(item.function, task.id)">
                        <v-btn icon>
                          <v-icon color="grey">mdi-{{ item.button }}</v-icon>
                        </v-btn>
                        {{ item.title }}
                      </v-list-item-title>
                    </v-list-item>
                    <v-list-item>
                      <v-list-item-title>
                        <div role="button" @click="handle = true">
                          <v-btn icon>
                            <v-icon color="grey">mdi-sort</v-icon>
                          </v-btn>
                          Sắp Xếp
                        </div>
                      </v-list-item-title>
                    </v-list-item>
                  </v-list>
                </v-menu>
              </template>
            </v-list-item>
            <v-divider></v-divider>
            <v-dialog ref="dialog" v-if="dialog === 0" v-model="task.modal" :return-value.sync="task.title" persistent width="290px">
              <v-card>
                <v-card-title>Chỉnh Sửa Công Việc</v-card-title>
                <v-text-field v-model="task.title" @keyup.enter="saveTask($refs, i, task.title, 'Cập Nhật Công Việc Thành Công!')" class="pa-5"></v-text-field>
                <v-btn text color="primary" @click.stop="task.modal = false">Hủy Bỏ</v-btn>
                <v-btn text color="primary" @click.stop="saveTask($refs, i, task.title, 'Cập Nhật Công Việc Thành Công!')">Lưu</v-btn>
              </v-card>
            </v-dialog>

            <v-dialog ref="dialog" v-else v-model="task.modal" :return-value.sync="task.dueDate" persistent width="290px">
              <v-date-picker v-model="task.dueDate" scrollable :min="today()">
                <v-btn text color="primary" @click.stop="task.modal = false">Hủy</v-btn>
                <v-btn text color="primary" @click.stop="saveTask($refs, i, task.dueDate, 'Đã Đặt Hẹn Ngày Công Việc Thành Công!')">Lưu</v-btn>
              </v-date-picker>
            </v-dialog>
          </div>
        </transition-group>
      </draggable>
    </v-list>

    <div v-if="tasks.length === 0" class="my-auto text-center green--text">
      <v-icon x-large class="green--text">mdi-check-all</v-icon>
      <h1>Hiện Tại Không Có Ghi Chú Nào!</h1>
    </div>

    <v-snackbar v-model="snackbar.active">
      {{ snackbar.text }}

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar.active = false"> Đóng </v-btn>
      </template>
    </v-snackbar>

    <div class="handle-div text-center" v-if="handle">
      <v-btn fixed bottom close color="primary" @click="handle = false"> Hoàn Thành Sắp Xếp </v-btn>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";

export default {
  name: "Todo",
  components: {
    draggable,
  },
  data() {
    return {
      handle: false,
      newTaskTitle: "",
      tasks: [],
      snackbar: {
        active: false,
        text: String,
      },
      dialog: Number,
      sideMenu: [{ title: "Delete", button: "delete", function: "deleteTask" }],
    };
  },
  mounted() {
    if (localStorage.tasks) {
      this.tasks = JSON.parse(localStorage.tasks);

      const today = new Date(`${this.today()} GMT-0300`);

      this.tasks.forEach((e) => {
        const taskDueDate = new Date(`${e.dueDate} GMT-0300`);

        return today > taskDueDate ? (e.expired = true) : (e.expired = false);
      });
    }
  },
  methods: {
    today() {
      // Gets the current date and returns "XXXX-XX-XX"
      const rawToday = new Date();
      const rawMonth = () => {
        const month = rawToday.getMonth() + 1;

        return month < 10 ? `0${month}` : month;
      };

      return `${rawToday.getFullYear()}-${rawMonth()}-${rawToday.getDate()}`;
    },
    computedDue(due) {
      // Gets the task due date and returns "month, XX"
      return new Date(`${due} GMT-0300`).toLocaleString("en-US", { month: "short", day: "2-digit" });
    },
    handleFnCall(fnName, taskId) {
      return this[fnName](taskId);
    },
    snackBar(message) {
      this.snackbar.text = message;

      return (this.snackbar.active = true);
    },
    addTask() {
      if (this.newTaskTitle === "") {
        // Checks wether task title was typed and actives the warining snackbar if it isn't
        return this.snackBar("Lưu Ghi Chú Và Không Được Để Trống!");
      } else {
        // Creates the new task object...
        const idDate = Date.parse(new Date()) + (Math.floor(Math.random() * 10000000000000) + 1);
        const newTask = {
          id: idDate,
          title: this.newTaskTitle,
          dueDate: null,
          expired: false,
          done: false,
          modal: false,
        };
        // ... then pushes it into 'tasks' array | Shows the snackbar "ADDED"
        return this.tasks.push(newTask), (this.newTaskTitle = ""), this.snackBar("Đã Thêm Ghi Chú Thành Công!");
      }
    },
    saveTask(refs, index, obj, message) {
      // Gets refs, object to save (title or due date), message and saves the task
      return refs.dialog[index].save(obj), this.snackBar(message);
    },
    doneTask(taskID) {
      // Marks the task as completed and shows the snackbar "DONE"
      const task = this.tasks.filter((task) => task.id === taskID)[0];

      task.done = !task.done;
      return task.done && task.done === true ? this.snackBar("Công Việc Đã Hoàn Thành!") : this.snackBar("Công Việc Chưa Hoàn Thành!");
    },
    deleteTask(taskID) {
      // Deletes the task and shows the snackbar "DELETED"
      this.tasks = this.tasks.filter((task) => task.id !== taskID);

      return this.snackBar("Đã Xóa Ghi Chú Thành Công!");
    },
  },
  watch: {
    tasks: {
      handler(addTask) {
        localStorage.tasks = JSON.stringify(addTask);
      },
      deep: true,
    },
  },
};
</script>

<style lang="less">
.sortable {
  &-drag {
    opacity: 0;
  }
}

.flip-list-move {
  transition: transform 0.5s;
}

.ghost {
  border-left: 4px solid #90caf9;
  box-shadow: 10px 10px 5px -1px hsla(0, 0, 0, 0.14);
  opacity: 0.7;
}

.handle-div {
  margin-left: -256px;

  @media screen and (max-width: 776px) {
    margin-left: -155px;
  }
}
</style>
