egg

```js
  async showapi() {
    const { ctx } = this;
    // 获取post 请求参数
    let count = ctx.request.body.count;
    const url = 'https://ip.taobao.com/outGetIpInfo';
    const result = await ctx.curl(url,{
      method: 'GET', // 设置请求方式 默认是GET
      dataType: 'json',
      // contentType: 'json', // 默认是 form
      data :{
        accessKey: 'alibaba-inc',
        ip: '11.11.11.11'
      },
    });
    ctx.body = result && result.res&& result.res.data 
  };
```

```js
async axiosapi() {
    const { ctx } = this;
    let data;
    const url = '/outGetIpInfo/';
    const params = { 
      accessKey: 'alibaba-inc',
      ip: '11.11.11.11'
    };

    const instance = ctx.axios.create({
      baseURL: 'https://ip.taobao.com/',
      timeout: 5000,
      headers: {
        'Content-Type':'application/xml',
        'X-API-KEY':'1d93a8763d9d49bd995a4285cc7ac7aa',
      }
    });
    const result = await instance.get(url,{      
      params : params,
    }).then( res =>{
      console.log("res start++++++++++++++");
      console.log(res.data);
      console.log("res end++++++++++++++");
      data = res.data
    })
    .catch(err =>  {
        console.log("++++++++++++++error++++++++++++++");
        console.log('err=',err)
    });;
    ctx.body = data 
  };
  
```
  
  ```js
  
  async onlineGetXtOpensea(){
    const url = 'wyvern/v1/orders/';
    const params = this.ctx.query || {};
    const instance = ctx.axios.create({
      baseURL: 'https://api.opensea.io/',
      timeout: 5000,
      headers: {
        'Content-Type':'application/xml',
        'X-API-KEY':'1d93a8763d9d49bd995a4285cc7ac7aa',
      }
    });
    let list = [], errList, orderSeach, orders,  accountAddress = this.openSeaConfig.accountAddress;    
    
    const result = await instance.get(url,{      
      params : params,
    }).then( res =>{
        this.logger.info("res orders start++++++++++++++");
        this.logger.info(res.data);
        this.logger.info("res orders end++++++++++++++");
        data = res.data
        orderSeach  = res || res.data;
        orders =  orderSeach.orders ||[];
        if(orders&&orders.length>0){
          orders.forEach(order=>{
            this.openSeaConfig.seaport.fulfillOrder({ order, accountAddress }).then(transactionHash=>{
              list.push(JSON.parse(transactionHash))
              console.log('JSON.parse(transactionHash)====',JSON.parse(transactionHash))
            })
          })
        }
      })     
      .catch(err =>  {
          errList = err;
          this.logger.error('err=',err)
      });

    return new Promise((resolve, reject) => {
      let hasOrders = orders && orders.length > 0;
      if(hasOrders){
        resolve({ success: true, list: list , msg: 'search success'});
      }else{
        reject({ success: false, errList: errList, msg: 'search error orders is null', order: orderSeach});
      } 
    });
  }
  ```
