<template>
  <div class="city">
    <div class="search_wrap">
      <div class="search">
        <i class="fa fa-search"></i>
        <input v-model="city_val" type="text" placeholder="输入城市名" />
      </div>
      <button @click="$router.push({name:'address',params:{city:city}})">取消</button>
    </div>
    <div style="height:100%" v-if="searchList.length==0">
      <div class="location">
        <Location @click="selectCity(city)" :address="city" />
      </div>
      <Alphabet @selectCity="selectCity" :cityInfo="cityInfo" :keys="keys" ref="allcity" />
    </div>
    <div class="search_list" v-else>
      <ul>
        <li
          @click="selectCity(item)"
          v-for="(item,index) in searchList"
          :key="index"
        >{{item.name}}</li>
      </ul>
    </div>
  </div>
</template>
<script>
import Location from "../components/Location";
import Alphabet from "../components/Alphabet";
export default {
  name: "city",
  data() {
    return {
      city_val: "",
      cityInfo: null,
      keys: [],
      allCities: [],
      searchList: []
    };
  },
  components: {
    Location,
    Alphabet
  },
  computed: {
    city() {
      return (
        this.$store.getters.location.addressComponent.city ||
        this.$store.getters.location.addressComponent.province
      );
    }
  },
  created() {
    this.getCityInfo();
  },
  methods: {
    // 获取城市
    getCityInfo() {
      // 接口数据
      // this.$axios("/api/posts/cities")
      // 模拟数据
      // this.$axios("cities.json")
       fetch("cities.json",{
        methods:'get',
        headers: {
          "Content-type": "application/json"
        }
      }) .then(res => res.json())
        .then(res => {
          console.log(res);
          this.cityInfo = res;
          // 处理key 计算key
          this.keys = Object.keys(res);
          // console.log(this.keys);
          // 移出掉hotcities这个key移出
          this.keys.pop();
          // key排序
          this.keys.sort();
          this.$nextTick(() => {
            this.$refs.allcity.initScroll();
          });
          // 存储所有城市，用来搜索过滤
          this.keys.forEach(key => {
            // console.log(key);
            this.cityInfo[key].forEach(city => {
              // console.log(city);
              this.allCities.push(city);
            });
          });
        })
        .catch(err => {
          console.log(err);
        });
    },
    // 选中城市
    selectCity(city) {
      console.log(city);
      this.$router.push({ name: "address", params: { city: city } });
    },
    // 搜索城市
    searchCity(city) {
      if (!this.city_val) {
        // 如果搜索框为空，数组置空
        this.searchList = [];
      } else {
        // 根据输入内容在所有城市中过滤出相关城市并存入数组
        this.searchList = this.allCities.filter(item => {
          return item.name.indexOf(this.city_val) != -1;
        });
        // 结果城市输出
        // console.log(this.searchList);
      }
    }
  },
  watch: {
    city_val() {
      console.log(this.city_val);
      this.searchCity(this.city_val);
    }
  }
};
</script>
<style scoped>
.city {
  width: 100%;
  height: 100%;
  overflow: auto;
  box-sizing: border-box;
  padding-top: 45px;
}
.search_wrap {
  position: fixed;
  top: 0;
  height: 45px;
  width: 100%;
  background: #fff;
  box-sizing: border-box;
  padding: 3px 16px;
  display: flex;
  justify-content: space-between;
}
.search {
  background-color: #eee;
  border-radius: 10px;
  line-height: 40px;
  width: 85%;
  box-sizing: border-box;
  padding: 0 16px;
}
.search input {
  background: #eee;
  outline: none;
  border: none;
  margin-left: 5px;
}
.search_wrap button {
  outline: none;
  color: #009eef;
  background-color: transparent;
  border: 0;
  font-size: inherit;
  /* font-weight:bold; */
}
/* .search_wrap button{
  box-shadow: none;
  color: #009eef;
  display: inline-block;
  padding: 0;
  outline: none;
} */
.location {
  background: #fff;
  padding: 8px 16px;
  height: 65px;
  box-sizing: border-box;
}

.search_list {
  background: #fff;
  padding: 5px 16px;
}
.search_list li {
  padding: 10px;
  border-bottom: 1px solid #eee;
}
</style>

