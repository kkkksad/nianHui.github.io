<template>
  <div class="c-Publicity">
    <el-carousel height="50px" :autoplay="true" indicator-position="none" arrow="never" :interval="3000">
      <el-carousel-item v-for="item in message" :key="item.key">
        <div class="item" :class="{ actiname: item.key === 0 }">
          <span v-if="item.title" class="title"> {{ item.title }}</span>
          <span v-if="item.value" class="value">
            <!-- {{ item.value }} -->
            {{ getPersonName(item.value) }}
          </span>
        </div>
      </el-carousel-item>
    </el-carousel>
  </div>
</template>
<script>
import { conversionCategoryName } from '@/helper/index';

export default {
  name: 'Publicity',
  data() {
    return {
      allPeople: [],

    }
  },
  computed: {
    config() {
      return this.$store.state.config;
    },
    result() {
      return this.$store.state.result;
    },
    message() {
      // console.log(this);
      const { result, config } = this;
      const fields = Object.keys(config);
      let message = [{ key: 0, title: config.name }];
      fields.forEach((item, index) => {
        let label = conversionCategoryName(item);
        if (result[item] && config[item] > 0) {
          message.push({
            key: index + 1,
            title: `${label}抽奖结果:`,
            value: `${result[item].length > 0 ? result[item].join('、') : '暂未抽取'
              }`
          });
        }
      });

      return message;
    },

  },
  watch: {
    '$store.state.list': {
      handler(newList) {
        // console.log(newList);
        this.allPeople = newList;
      },
      immediate: true, // 使得一开始就更新
    },
  },
  methods: {
    getPersonName(value) {
      // console.log(value);

      if (value) {
        const result = value.replace(/、/g, ' ').split(' ');
        const names = result.map(num => {
          const person = this.allPeople[num - 1]; // 获取对应的人员
          return person ? person.name : ''; // 返回人员的名字，如果没有有效人员则返回空字符串
        });
        const finalResult = names.filter(name => name).join(','); // 使用空格连接名字
        return finalResult; // 如果没有有效的人物，返回空字符串
      }
      return ''; // 如果 value 不存在，返回空字符串
    }
  }
};
</script>
<style lang="scss">
.c-Publicity {
  height: 100%;
  // width: 1000px;
  background-color: rgba(255, 255, 255, 0.1);
  margin: 0 auto;
  position: relative;
  overflow: hidden;

  .el-carousel {
    width: 80vw;
    margin: 0 auto;
  }

  .item {
    text-align: center;
    color: #fff;
    font-size: 16px;
    display: flex;
    justify-content: center;

    .title {
      color: #ccc;
    }

    .value {
      white-space: nowrap;
      // /* 禁止换行 */
      overflow: hidden;
      // /* 超出部分隐藏 */
      text-overflow: ellipsis;
      margin-left: 10px;
    }

    &.actiname {
      .title {
        color: red;
        font-size: 20px;
      }
    }
  }
}
</style>
