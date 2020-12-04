#### `store.js` 文件
```javascript
import Vue from 'vue';
import Vuex from 'vuex';
import app from './modules/app';

Vue.use(Vuex);

export default new Vuex.Store({
  modules: {
    app
  },
  state: {},
  mutations: {},
  actions: {},
  getters: {}
});
```

#### `vuex` 中 `app` 模块代码：

```javascript
export default {
  namespaced: true, // ⭐️⭐️⭐️⭐️⭐️
  state: {
    message: {}
  },
  mutations: {
    SET_MESSAGE(state, message) {
      state.message = message;
    }
  },
  actions: {},
  getters: {
    message: state => state.message
  }
};
```

#### 在 `.vue` 文件中的引用

```javascript
import { mapMutations, mapGetters } from 'vuex';

export default {
  computed: {
    ...mapGetters({
      message: 'app/message'
    })
  },
  methods: {
    onSelectMessage(){
      this.setMessage()
    }
    ...mapMutations({
      setMessage: 'app/SET_MESSAGE'
    })
  }
};
```