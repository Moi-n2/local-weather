# Local-weather

[live deploy](https://glistening-beijinho-02ff9e.netlify.app/)

部署在netlify的小demo, 用到了vue3/vite + tailwind +高德api，可用来查询当地天气，收藏城市放在localstorage

### some notes:

1. transition 切换组件淡出淡入
   ```
    <Transition name="modal-outer" v-show="modalActive">
     ...
   </Transition>

   .modal-outer-enter-active,
    .modal-outer-leave-active{
      transition: opacity .3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
    }
    
    .modal-outer-enter-from,
    .modal-outer-leave-to{
      opacity: 0;
    }
   ```
   路由页面组件切换动态
   ```
   <RouterView class="flex-1" v-slot="{ Component }">
      <transition name="page">
        <component :is="Component"></component>
      </transition>
    </RouterView>
   ```
3.  teleport 将提示组件插入body元素内
    ```
    <Teleport to="body">
    ```
4. 骨架屏 tailwind
   ```
   <div class="animate-pulse bg-gradient-to-r from-gray-100">
    &nbsp;
    </div>
   ```
5. suspense 切换异步组件
   ```
  <Suspense>
      <template #default>
        <AsyncCityView />
      </template>
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense> 
   ```
6. citylist 多次调用接口获取数据
   forEach + promise.all
   ```
   const requests = []

  savedCities.value.forEach((city) => { 
    requests.push(fetch(`https://restapi.amap.com/v3/weather/weatherInfo?key=79f62c120f52839c6f67d6cd0280e50f&city=${city.adcode}&extensions=base`).then(res => res.json()).then(data => { 
      data.lives[0].district = city.district
      return data.lives[0]
    }))
  })

  cityLists.value = await Promise.all(requests)
   ```
7. 路由守卫+ 路由meta属性，动态修改document.title

  ```
    router.beforeEach((to, from, next) => { 
    document.title = `${to.params.district ? `${to.params.district}` : to.meta.title} | The Local Weather`
  
    next()
  })
  ```
