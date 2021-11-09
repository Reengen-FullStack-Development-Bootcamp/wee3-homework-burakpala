<template>
  <div class="hello">
    <div class="container">
      <form class="navbar-form m-2">
        <div class="input-group no border">
          <input
            type="text"
            value=""
            class="form-comntrol"
            placeholder="Search..."
            v-model="stock"/>
          <button
            @click="update(stock)"
            class="btn btn-white btn-round btn-just-icon"
          >
            <i class="material-icons ml-3">search</i>
          </button>
        </div>
      </form>
      <!--information-->
      <div class="row">
        <div class="col-md-3">
          <div class="card card-stats card-background">
            <div class="card-header card-header-icon">
              <div class="card-icon">
                <i class="material-icons">content_copy</i>
              </div>
              <p class="card-category">Beta</p>
              <h4 class="card-titile">{{this.beta}}</h4>
            </div>
          </div>
        </div>
        <div class="col-md-3">
          <div class="card card-stats card-background">
            <div class="card-header card-header-icon">
              <div class="card-icon">
                <i class="material-icons">store</i>
              </div>
              <p class="card-category">CEO</p>
              <h4 class="card-titile">{{this.CEO}}</h4>
            </div>
          </div>
        </div>
        <div class="col-md-3">
          <div class="card card-stats card-background">
            <div class="card-header card-header-icon">
              <div class="card-icon">
                <i class="material-icons">info</i>
              </div>
              <p class="card-category">Company.Name</p>
              <h4 class="card-titile">{{this.companyName}}</h4>
            </div>
          </div>
        </div>
        <div class="col-md-3">
            <div class="card card-stats card-background">
            <div class="card-header card-header-icon">
              <div class="card-icon">
                <i class="material-icons">storage</i>
              </div>
              <p class="card-category">Sector</p>
              <h4 class="card-titile">{{this.sector}}</h4>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-12">
          <md-list>
            <md-list-item class="col-md-3">
              <md-icon>timeline</md-icon>
              <span class="left">Price {{this.price}}</span>
              <span class="right"> {{this.priceBookValueRatio}}</span>
            </md-list-item>

              <md-list-item class="col-md-3">
              <md-icon>attach_money</md-icon>
              <span class="left">ROA {{this.ROA}}%</span>
              <span class="right"> {{this.pricetosales}}</span>
            </md-list-item>

              <md-list-item class="col-md-3">
              <md-icon>attach_money</md-icon>
              <span class="left">ROE {{this.ROE}}%</span>
              <span class="right"> {{this.priceEarningsRatio}}</span>
            </md-list-item>

             <md-list-item class="col-md-3">
              <md-icon>bar_chart</md-icon>
              <span class="">Gross Profit Mar {{this.grossProfitMargin}}%</span>
            </md-list-item>
          </md-list>
        </div>
      </div>
      <div class="row mt-5">
        <div class="col-md-4">
         <div class="card card-profile border-0">
           <div class="card-avatar">
             <img class="img" :src="image" alt="">
           </div>
           <div class="card-body">
              <h4 class="card-title no-outline">
                {{this.companyName}}
              </h4>
              <p>
                {{this.description}}
              </p>
           </div>
         </div>
        </div>
        <div class="col-md-8">
          <div id="CandleStick"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Plotly from 'plotly.js-dist'
export default {
  name: "StockInfo",
  props: {
    msg: String,
  },
  data() {
    return {
      stock: '',
      beta: '',
      infoCompany:'',
      CEO:'',
      sector:'',
      companyName:'',
      price:'',
      marketCap:'',
      ROA:'',
      ROE:'',
      priceBookValueRatio:'',
      grossProfitMargin:'',
      pricetosales:'',
      priceEarnings:'',
      image:'',
      description:'',
      open:[],
      close:[],
      high:[],
      low:[],
      x:[],
      opentemp:'',
      xi:'',
      trace1:{}


    };
  },
  methods: {
    update(stock) {
      //console.log(stock);
      this.getInfo(stock)
      this.getCompanyValue(stock)
      this.getChart(stock)
    },
    getChart(stock){
      axios.get('https://financialmodelingprep.com/api/v3/historical-price-full/AAPL?timeseries=5&apikey=8d221b7bf1656093515ebc01808da2e4')
      .then(res=>{
        this.open=[]
        this.close=[]
        this.high=[]
        this.low=[]
        this.x=[]
        this.xi=''
        this.opentemp=res.data.historical
        for(this.xi of this.opentemp){
          this.open.push(this.xi.open)
          this.close.push(this.xi.close)
          this.high.push(this.xi.high)
          this.low.push(this.xi.low)
          this.x.push(this.xi.date)

        } 
        console.log(this.low)
      })
      .catch(err=>console.log(err))

      var trace1 = {
  
  x: this.x, 
  
  close: this.close,
  
  decreasing: {line: {color: '#7F7F7F'}}, 
  
  high: this.high,
  
  increasing: {line: {color: '#17BECF'}}, 
  
  line: {color: 'rgba(31,119,180,1)'}, 
  
  low: this.low, 
  
  open: this.open,
  
  type: 'candlestick', 
  xaxis: 'x', 
  yaxis: 'y'
};


var data = [trace1];

var layout = {
  dragmode: 'zoom', 
  margin: {
    r: 10, 
    t: 20, 
    b: 20, 
    l: 30
  }, 
  showlegend: false, 
  xaxis: {
    autorange: true, 
    rangeslider: {range: [this.x[0],this.x[1000]]}, 
    title: 'Date', 
    type: 'date'
  }, 
  yaxis: {
    autorange: true, 
    range:[Math.min(...this.close)-10, Math.max(...this.close)+20],
    type: 'linear'
  },
  
  annotations: [
    {
      x: '2017-01-31',
      y: 0.9,
      xref: 'x',
      yref: 'paper',
      text: 'largest movement',
      font: {color: 'magenta'},
      showarrow: true,
      xanchor: 'right',
      ax: -20,
      ay: 0
    }
  ],
  
  shapes: [
      {
          type: 'rect',
          xref: 'x',
          yref: 'paper',
          x0: '2017-01-31',
          y0: 0,
          x1: '2017-02-01',
          y1: 1,
          fillcolor: '#d3d3d3',
          opacity: 0.2,
          line: {
              width: 0
          }
      }
    ]
};

Plotly.newPlot('CandleStick', data, layout);
    },
    getCompanyValue(stock){
      axios.get('https://financialmodelingprep.com/api/v3/financial-ratios/AAPL?apikey=8d221b7bf1656093515ebc01808da2e4')

      .then(res =>{
         this.ROA= parseFloat(res.data.ratios[0]
         .profitabilityIndicatorRatios.returnOnAssets *100).toFixed(2)

         this.ROE= parseFloat(res.data.ratios[0]
         .profitabilityIndicatorRatios.returnOnEquity *100).toFixed(2)

         this.grossProfitMargin= parseFloat(res.data.ratios[0]
         .profitabilityIndicatorRatios.grossProfitMargin *100).toFixed(2)

         this.priceBookValueRatio= parseFloat(res.data.ratios[0]
         .investmenValuationRatios.priceBookValueRatio).toFixed(2)

         this.pricetosales= parseFloat(res.data.ratios[0]
         .investmenValuationRatios.priceToSalesRatio).toFixed(2)

         this.priceEarnings= parseFloat(res.data.ratios[0]
         .investmenValuationRatios.priceEarningsRatio).toFixed(2)
         
      })
      .catch(err=>console.log(err))
    },
    getInfo(stock){
      console.log(stock)
      axios.get('https://financialmodelingprep.com/api/v3/company/profile/AAPL?apikey=8d221b7bf1656093515ebc01808da2e4')


      .then(res =>{
         this.infoCompany= res.data.profile
         this.beta=parseFloat(this.infoCompany.beta).toFixed(2)
         this.CEO=this.infoCompany.ceo 
         this.sector=this.infoCompany.sector
         this.companyName=this.infoCompany.companyName
         this.price=this.infoCompany.price
         this.marketCap=this.infoCompany.mktCap
         this.image=this.infoCompany.image
        this.description=this.infoCompany.description


      })
      .catch(err=>console.log(err))
      console.log(this.infoCompany)
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.card-background{
  background-color: rgba(19, 155, 121, 0.541);
  color: white;
}
.md-list{
  max-width: 100%;
  display: inline-block;
  vertical-align: top;
  border: 1px solid rgb(223, 196, 196);
}
.left{
  margin-right: 12px;
  margin-top: 0px;
}
.right{
  margin-left: 10px;
  margin-top: px;
}
</style>

