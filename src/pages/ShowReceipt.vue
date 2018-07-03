<template>
    <div class="page-container">
        <div class="page order-history-page" flex="dir:top box:first">
            <nav-bar
                title="查看发票"
            />
            <div class="page-content"  flex="dir:top box:first">
                <canvas id="the-canvas"></canvas>
            </div>
        </div>
    </div>
</template>
<script>
    import NavBar from '../components/NavBar';
    import Tool from '../utils/Tool';
    import PDFJS from 'pdfjs-dist';
    import { Toast } from 'mint-ui';
    export default {
        data () {
            return {
                pdfSrc:"",
                pdfDoc: null,
                scale: 0.9
            }
        },
        components:{
            NavBar,
        },
        methods:{
            renderPage (num) {
              let _this = this;
              this.pdfDoc.getPage(num).then(function (page) {
                var viewport = page.getViewport(_this.scale)
                let canvas = document.getElementById('the-canvas')
                canvas.height = viewport.height
                canvas.width = viewport.width

                // Render PDF page into canvas context
                var renderContext = {
                  canvasContext: canvas.getContext('2d'),
                  viewport: viewport
                }
                var renderTask = page.render(renderContext)
              })
            },
        },
        activated:function(){
            var id = this.$route.params.id;
            Tool.post("getInvoiceUrl",{id:id},(data)=>{
                if(data.code == 200){
                    this.pdfSrc = data.data
                }else{
                    Toast({
                        duration:1000,
                        message:data.msg,
                    })
                }
            })
            let _this = this;
            PDFJS.getDocument(this.pdfSrc).then(function (pdf) {
                _this.pdfDoc = pdf
                _this.renderPage(1)
            })
        },
    }
</script>
<style lang="less" scoped>
    .page-container{
        height:100%;
        position:absolute;
        width:100%;
    }
    .page{
        height:100%;
        position:absolute;
        width:100%;
        .page-content{
            background-color: #efefef;
            height:100%;
            overflow: auto;
        }
    }
</style>
