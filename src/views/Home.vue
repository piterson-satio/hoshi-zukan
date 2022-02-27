<template>
  <div class="home">
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <form @submit="parseData">
      <PrismEditor v-model="data" class="my-editor" :highlight="highlighter" :line-numbers="true" />
      <button class="btn-submit" type="submit">Analyze</button>
    </form>

    <div>
      <!-- {{ analyzedData }} -->
      <hr />
      <ul v-if="typeCount" class="list">
        <template v-for="(val, type, i) in typeCount">
          <li :key="type+i" v-if="val.count > 0">
            <strong :style="{color: val.color}">{{type}}</strong>: {{val.count}}
          </li>
        </template>
      </ul>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue';
import { PrismEditor } from 'vue-prism-editor';
import 'vue-prism-editor/dist/prismeditor.min.css';

export default {
  name: 'Home',
  components: {
    // HelloWorld,
    PrismEditor,
  },
  data() {
    return {
      data: '',
      analyzedData: [],
      typeList: [
        ['normal', '#A8A878'],
        ['grass', '#78C850'],
        ['fire', '#F08030'],
        ['water', '#6890F0'],
        ['electric', '#F8D030'],
        ['bug', '#A8B820'],
        ['flying', '#A890F0'],
        ['fighting', '#C03028'],
        ['ground', '#E0C068'],
        ['rock', '#B8A038'],
        ['steel', '#B8B8D0'],
        ['poison', '#A040A0'],
        ['psychic', '#F85888'],
        ['ghost', '#705898'],
        ['dark', '#705848'],
        ['ice', '#98D8D8'],
        ['dragon', '#7038F8'],
        ['fairy', '#EE99AC'],
      ],
      typeCount: null,
    };
  },
  methods: {
    highlighter(data) {
      return data;
    },
    parseData() {
      this.resetTypeCount();

      const tempData = this.data.split(/\r?\n/).reduce((res, curr) => {
        const trimmed = curr.trim();
        if (trimmed.length <= 0) return [...res];

        const [name, types] = trimmed.split(';');
        const [type1, type2] = types.split('/').map(type => type.trim().toLowerCase());

        const row = {
          name: name.trim(),
          type: [type1, type2],
        };

        this.typeCountIncrement(type1);
        this.typeCountIncrement(type2);

        return [...res, row];
      }, []);
      this.analyzedData = tempData;
    },
    resetTypeCount() {
      this.typeCount = this.typeList.reduce((res, curr) => {
        const data = {
          name: curr[0],
          count: 0,
          color: curr[1],
        };
        return { ...res, [curr[0]]: data };
      }, {});
    },
    typeCountIncrement(typeName) {
      if (typeName === '' || typeName === undefined || typeName === null) return;

      if (typeName && this.typeCount[typeName].count >= 0) {
        this.typeCount[typeName].count += 1;
      } else {
        this.typeCount[typeName] = {
          name: typeName,
          count: 1,
          color: '#000000',
        };
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.my-editor {
  max-width: 99%;
  background: #2d2d2d;
  color: #ccc;

  /* you must provide font-family font-size line-height. Example: */
  font-family: Fira code, Fira Mono, Consolas, Menlo, Courier, monospace;
  font-size: 14px;
  line-height: 1.5;
  padding: 5px;
  margin-bottom: 24px;
}

.btn-submit {
  margin-bottom: 16px;
}

.list {
  text-align: left;

  & > li {
    text-transform: capitalize;
  }
}

.bold {
  font-weight: 700;
}
</style>
