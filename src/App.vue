<script>
export default {
  name: "AppVue",
  created() {
    this.listOfSelections.push(this.initialOptions)
    this.selectedTargetParameter = this.targetParameters[0]
  },
  data() {
    return {
      listOfSelections: [],
      selectedTargetParameter: null,
      targetParameters: [
        "Выручка",
        "ВД",
        "GM1",
        "GM2",
        "GM3",
        "GM4",
        "ЧП",
        "Средняя цена",
        "Продажи (шт.)",
      ],
      initialOptions: {
        id: 0,
        selectorName: "Подразделениe",
        selectedOption: null,
        options: [
          {
            id: 1,
            selectorName: "Бизнес Сегмент",
            name: "Подразделение 1",
            selectedOption: null,
            options: [
              {
                id: 10,
                selectorName: "Бизнес Сфера",
                name: "Продажи",
                selectedOption: null,
                options: [
                  {
                    id: 11,
                    selectorName: "Нижний Уровень",
                    name: "Новые Авто (ОПА)",
                    selectedOption: null,
                    options: [
                      { id: 21, name: "Марка1" },
                      { id: 27, name: "Марка2" },
                      { id: 28, name: "Марка3" },
                    ]
                  },
                  {
                    id: 12,
                    selectorName: "Нижний Уровень",
                    name: "Вторичный Рынок (МПА)",
                    selectedOption: null,
                    options: [
                      { id: 22, name: "Выкуп" },
                      { id: 25, name: "Trade In" },
                      { id: 26, name: "Комиссия" }
                    ]
                  },
                  {
                    id: 13,
                    selectorName: "Нижний Уровень",
                    name: "Допоборудование (ОДО)",
                    selectedOption: null,
                    options: [
                      { id: 23, name: "ОДО" },
                    ]
                  },
                  {
                    id: 14,
                    selectorName: "Нижний Уровень",
                    name: "Финансовые Сервисы (ФС)",
                    selectedOption: null,
                    options: [
                      { id: 24, name: "ФС" },
                    ]
                  }
                ]
              }
            ]
          },
          {
            id: 2,
            selectorName: "Бизнес Сегмент",
            name: "Подразделение 2",
          }, 
          {
            id: 3,
            selectorName: "Бизнес Сегмент",
            name: "Подразделение 3",
          }
        ]
      }
    }
  },
  methods: {
    getSubSelector(level, options) {
      const selectedOptionId = this.listOfSelections[level].selectedOption
      return options.filter(selector => selector.id === selectedOptionId).shift()
    },
    removeLines(level) {
      for (let selector of this.listOfSelections.slice(level + 1)) {
        if (selector.line) selector.line.remove()
      }
    },
    updateSubcategories(level, options, optionId, event) {
      this.listOfSelections[level].selectedOption = optionId

      if (options) {
        const subSelector = this.getSubSelector(level, options)
        this.removeLines(level)
        this.listOfSelections.splice(level + 1)

        if (subSelector.options) {
          let newSelection = {
            id: this.listOfSelections[level].id + 1,
            selectorName: subSelector.selectorName,
            selectedOption: null,
            options: subSelector.options,
          }
          this.listOfSelections.push(newSelection)

          // Draw connection line
          this.$nextTick(() => {
            const groups = document.getElementsByClassName("group")
            const group = groups[groups.length - 1]
            const groupHeader = group.childNodes[0]
            const line = new LeaderLine(event.target, groupHeader)
            line.setOptions({
              color: 'black',
              size: 1,
              startPlug: 'behind',
              endPlug: 'arrow1',
            })
            this.listOfSelections[this.listOfSelections.length - 1].line = line
          })
        }
      }
    },
  },
}
</script>

<template>
  <main>
    <header class="header">
      <h1>Choose Level</h1>
      <div class="target-parameter-container">
        <label for="target-parameter">Choose Parameter</label>
        <select id="target-parameter" v-model="selectedTargetParameter">
          <option v-for="parameter in targetParameters" :key="parameter" :value="parameter">
            {{ parameter }}
          </option>
        </select>
      </div>
    </header>

    <div id="selector-container">
      <template v-for="(selection, index) in listOfSelections" :key="selection.id" :value="selection.id">
        <div class="group-container">
          <div class="group">
            <h2 :for="`${selection.id}`">{{ selection.selectorName }}</h2>
            <div 
              v-for="option in selection.options" :key="option.id" :value="option.id"
            >
              <div 
                class="option"
                :class="{'selectedOption': selection.selectedOption === option.id}"
                @click="updateSubcategories(index, selection.options, option.id, $event)"
              >
                {{ option.name }}
              </div>
            </div>
          </div>
        </div>
      </template>
    </div>
  </main>
</template>

<style>
.header {
  height: 200px;
  padding-left: 48px;
  padding-right: 48px;
  padding-top: 50px;
  padding-bottom: 25px;
}
.header h1 {
  font-size: 24px;
  line-height: 20px;
  font-weight: 500;
  margin-bottom: 19px;
}
#selector-container {
  display: flex;
  flex-direction: row;
  height: 330px;
  gap: 4rem;
  width: fit-content;
  min-width: 100%;
  padding: 1rem;
  background-color: #F4F4F4;
  padding-left: 48px;
  padding-right: 48px;
  padding-top: 41px;
}
.group {
  display: flex;
  flex-direction: column;
  width: 258px;
  min-height: 120px;
  background-color: #fff;
  border-radius: 17px;
  padding: 12px;
}
.group-container {
  position: relative;
}
.group h2 {
  font-size: 12px;
  line-height: 18px;
  font-weight: 500;
}
.target-parameter-container {
  display: flex;
  flex-direction: column;
  width: 180px;
}
.target-parameter-container label {
  color: #737B8A;
  font-weight: 600;
  font-size: 14px;
  line-height: 20px;
  text-transform: uppercase;
}
.target-parameter-container select {
  width: 180px;
  height: 48px;
  border: 1px solid #CDD1DB;
  outline: none;
  padding: 12px 16px 12px 16px;
  font-weight: 500;
  font-size: 14px;
  line-height: 20px;
  border-radius: 8px;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  background-image: url('src/assets/CaretDown1.png');
  background-position: calc(100% - 10px) center;
  background-repeat: no-repeat;
  background-size: 24px 24px;
}
.option {
  display: flex;
  align-items: center;
  padding: 0 15px;
  width: 100%;
  height: 32px;
  background-color: #F3F1F1;
  border-radius: 17px;
  font-size: 12px;
  line-height: 18px;
  font-weight: 500;
  cursor: pointer;
  margin-top: 4px;
  margin-bottom: 4px;
}
.selectedOption {
  border: 1px solid;
}
</style>