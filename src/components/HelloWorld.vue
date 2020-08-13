<template>
    <canvas id="c" ref="c" :width="cWidth" :height="cHeight" ></canvas> 
    <!-- <button @click="canvasDraw">click</button> @click="animate()" -->
</template>

<script>
export default {
    props:{
        height:Number,
        width:Number
    },
    data:function(){
        return{
            cHeight:500,
            cWidth:1000,
            circleNumbers:500 ,
            vueCanvas: null,
            maxRadius:30,
            defaultRadius:5,
            detectRadius:50,
            maxDetectRadius:300,
            defaultDetectRadius:50,

            mouseDown:false,


            circles:[],
            deltas:[],

            mouse:{
                x:undefined,
                y:undefined,
            },
            // x:200,
            // y:200,
            // dx:10,
            // dy:10,
            // dColorR:-1,
            // dColorG:-1,
            // dColorB:-1,

            // radius:30,
            // colorHexR:0,
            // colorHexG:0,
            // colorHexB:0,
        }
    },
    mounted(){
        this.cHeight=window.innerHeight;
        this.cWidth=window.innerWidth;
        window.onresize = () => {
            this.cHeight=(window.innerHeight)
            this.cWidth=(window.innerWidth)
        }
        var c = document.getElementById("c");
        var ctx = c.getContext("2d");
        this.vueCanvas = ctx;
        var tmp = this;
        window.addEventListener('mousemove', function(event){
            tmp.mouse.x = event.x;
            tmp.mouse.y = event.y;
        });
        window.addEventListener('mousedown', function(event){
            event;
            tmp.mouseDown = true;
        });
        window.addEventListener('mouseup', function(event){
            event;
            tmp.mouseDown = false;
        });
        // this.x = Math.random() * innerWidth;
        // this.y = Math.random() * innerHeight;

        for(var i=0; i < this.circleNumbers; i++)
        {
            var circle = {
                x:0,
                y:0,
                radius:this.defaultRadius,//+Math.floor(Math.random() * 30),
                colorHexR:0,
                colorHexG:0,
                colorHexB:0,
            }

            circle.x = Math.random() * (window.innerWidth - circle.radius * 2) + circle.radius;
            circle.y = Math.random() * (window.innerHeight - circle.radius * 2) + circle.radius;

            var h = 217;
            var s = Math.random();
            var v = 1.00;
            var hsv = this.HSVtoRGB(h,s,v);

            // console.log(hsv);

            circle.colorHexR = hsv.b
            circle.colorHexG = hsv.g
            circle.colorHexB = hsv.r

            this.circles.push(circle);


            // var index = Math.floor(Math.random() * 3);
            // // console.log(index);
            // var color = 100+ Math.floor((Math.random() * 150));
            // console.log(color);

            // switch(index)
            // {
            //     case 0:
            //         circle.colorHexR = color;
            //         // circle.colorHexG = color;
            //         break;
            //     case 1:
            //         circle.colorHexG = color;
            //         // circle.colorHexB = color;
            //         break;
            //     case 2:
            //         // circle.colorHexR = color;
            //         circle.colorHexB = color;
            //         break;
            //     default:
            //         break;
            // }
            

            var delta = {
                // dx:10,
                // dy:10,
                dx:Math.random()-0.5,//Math.floor(((Math.random()-0.5)*2)*3),
                dy:Math.random()-0.5,//Math.floor(((Math.random()-0.5)*2)*3),
                dColorR:-1,
                dColorG:-1,
                dColorB:-1,
            }
            this.deltas.push(delta);
        }
         this.animate()
    },
    methods:{
        clear:function(){
            this.vueCanvas.clearRect(0, 0, window.innerWidth, window.innerHeight);
        },


        canvasDraw:function(){    
            this.clear()       
            this.vueCanvas.fillRect(100, 100, 20, 20);
        },
        drawLine:function(){
            this.vueCanvas.fillStyle = 'rgba(255,0,0,0.5)'
            this.vueCanvas.beginPath();
            this.vueCanvas.moveTo(50,300);
            this.vueCanvas.lineTo(300,100);
            this.vueCanvas.lineTo(400,300);
            this.vueCanvas.strokeStyle ="blue";
            this.vueCanvas.stroke();
        },
        drawArc:function(circle){
            this.vueCanvas.beginPath();
            this.vueCanvas.arc(circle.x,circle.y,circle.radius,0,Math.PI *2,false);
            // this.vueCanvas.strokeStyle = `rgb(
            //     ${this.colorHexB},
            //     ${this.colorHexR},
            //     ${this.colorHexG})`;
            // this.vueCanvas.lineWidth = 2;
            // this.vueCanvas.stroke();
            this.vueCanvas.fillStyle = `rgb(
                ${circle.colorHexR},
                ${circle.colorHexG},
                ${circle.colorHexB})`;
            this.vueCanvas.fill();
        },

        update:function(circle,delta){
            // if(circle.colorHexR == 0 ||circle.colorHexR == 255)
            //     delta.dColorR = -delta.dColorR;
            // if(circle.colorHexG == 0 ||circle.colorHexG == 255)
            //     delta.dColorG = -delta.dColorG;
            // if(circle.colorHexB == 0 ||circle.colorHexB == 255)
            //     delta.dColorB = -delta.dColorB;    

            // circle.colorHexR += delta.dColorR;
            // circle.colorHexG += delta.dColorG;
            // circle.colorHexB += delta.dColorB;
            
            if(circle.x + circle.radius > this.cWidth || circle.x - circle.radius < 0)
            {
                delta.dx = -delta.dx;
            }

            if(circle.y + circle.radius > this.cHeight || circle.y - circle.radius < 0)
            {
                delta.dy = -delta.dy;
            }

            circle.x+=delta.dx;
            circle.y+=delta.dy;

            if(this.mouse.x - circle.x < this.detectRadius && this.mouse.x - circle.x > -this.detectRadius && this.mouse.y - circle.y < this.detectRadius && this.mouse.y - circle.y > -this.detectRadius){
                circle.x-=delta.dx*0.7;
                circle.y-=delta.dy*0.7;
                if(circle.radius <= this.maxRadius)
                { 
                    circle.radius += 3;
                }
            }
            else{
                if(circle.radius > this.defaultRadius)
                circle.radius -= 1;
            }

            if(this.mouseDown){
                if(this.detectRadius <= this.maxDetectRadius)
                    this.detectRadius += 0.005;
            }
            else{
                if(this.detectRadius >= this.defaultDetectRadius)
                    this.detectRadius -= 0.005;
            }
        },

        animate:function(){
            
            requestAnimationFrame(this.animate);
            this.clear();
            for(var index in this.circles)
            {
                this.drawArc(this.circles[index]);
                this.update(this.circles[index],this.deltas[index]);
            }
            // this.update();

        

        },
        HSVtoRGB:function(h, s, v) {
            var r, g, b, i, f, p, q, t;
            if (arguments.length === 1) {
                s = h.s, v = h.v, h = h.h;
            }
            i = Math.floor(h * 6);
            f = h * 6 - i;
            p = v * (1 - s);
            q = v * (1 - f * s);
            t = v * (1 - (1 - f) * s);
            switch (i % 6) {
                case 0: r = v, g = t, b = p; break;
                case 1: r = q, g = v, b = p; break;
                case 2: r = p, g = v, b = t; break;
                case 3: r = p, g = q, b = v; break;
                case 4: r = t, g = p, b = v; break;
                case 5: r = v, g = p, b = q; break;
            }
            return {
                r: Math.round(r * 255),
                g: Math.round(g * 255),
                b: Math.round(b * 255)
            };
        }
    }
}
</script>
<style scoped>
canvas{
    background-color: black;
    display: block;  /* prevent scrollbar */
}
</style>