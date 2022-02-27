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
        <li v-for="(val, type, i) in typeCount" :key="type+i" :class="{bold: val.count > 0}">
          {{type}}: {{val.count}}
        </li>
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
        'normal',
        'grass',
        'fire',
        'water',
        'electric',
        'bug',
        'flying',
        'fighting',
        'ground',
        'rock',
        'steel',
        'poison',
        'psychic',
        'ghost',
        'dark',
        'ice',
        'dragon',
        'fairy',
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
          name: curr,
          count: 0,
          color: '#000000',
        };
        return { ...res, [curr]: data };
      }, {});
    },
    typeCountIncrement(typeName) {
      if (typeName === '' || typeName === undefined || typeName === null) return;

      if (typeName && this.typeCount[typeName] >= 0) this.typeCount[typeName].count += 1;
      else {
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
