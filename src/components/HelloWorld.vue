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
            vueCanvas: null,                //canvus element

            cHeight:500,                    //window height
            cWidth:1000,                    //window width

            circleNumbers:800 ,            //how many circles
            maxRadius:50,                   //max radius before burst
            defaultRadius:4,                //default circle size
            detectRadius:50,                //current area of mouse point
            defaultDetectRadius:50,         //defaule area of mouse point
            maxDetectRadius:200,            //max area of mouse point
            burstRadius:120,                //how large when burst
            circleRespawnCountdown:300,     //respawn time in frames
            powRespawnCountdown:0,
            burstSpeed:8,                   //how quick circles burst

            powerUPCoolDown:20*60,
            powerUPlastingTimg:10*60,            


            mouseDown:false,                //clicking

            burstCount:0,
            powerUpCounts:1,

            powerUpTypes:["biggerAera","fasterBurst"],
            powerUps:[],
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
            // colorR:0,
            // colorG:0,
            // colorB:0,
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
                colorR:0,
                colorG:0,
                colorB:0,
                colorA:1,
                respawnCountdown:this.circleRespawnCountdown,
                alive:true
            }

            circle.x = Math.random() * (window.innerWidth - circle.radius * 2) + circle.radius;
            circle.y = Math.random() * (window.innerHeight - circle.radius * 2) + circle.radius;

            var h = 217/360;
            var s = Math.random();
            var v = 1.00;
            var hsv = this.HSVtoRGB(h,s,v);

            // console.log(h,s,v);

            circle.colorR = hsv.r
            circle.colorG = hsv.g
            circle.colorB = hsv.b

            this.circles.push(circle);
            

            var delta = {
                dx:Math.random()-0.5,//Math.floor(((Math.random()-0.5)*2)*3),
                dy:Math.random()-0.5,//Math.floor(((Math.random()-0.5)*2)*3),
                dColorR:-1,
                dColorG:-1,
                dColorB:-1,
            }
            this.deltas.push(delta);
        }
        
        for(var pow = 0 ; pow < this.powerUpCounts; pow++)
        {
            var powerUP = {
                x:0,
                y:0,
                radius:this.defaultRadius*10,//+Math.floor(Math.random() * 30),
                colorR:255,
                colorG:0,
                colorB:0,
                colorA:1,
                respawnCountdown:this.powRespawnCountdown,
                alive:false,
                powerUpTypes:this.powerUpTypes[0],
            }
            powerUP.x = Math.random() * (window.innerWidth - powerUP.radius * 2) + powerUP.radius;
            powerUP.y = Math.random() * (window.innerHeight - powerUP.radius * 2) + powerUP.radius;


            this.powerUps.push(powerUP);
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
            if(circle.radius > 0)
                this.vueCanvas.arc(circle.x,circle.y,circle.radius,0,Math.PI *2,false);
            // this.vueCanvas.strokeStyle = `rgb(
            //     ${this.colorB},
            //     ${this.colorR},
            //     ${this.colorG})`;
            // this.vueCanvas.lineWidth = 2;
            // this.vueCanvas.stroke();
            this.vueCanvas.fillStyle = `rgb(
                ${circle.colorR},
                ${circle.colorG},
                ${circle.colorB},
                ${circle.colorA})`;
            this.vueCanvas.fill();
        },

        update:function(circle,delta){
            // if(circle.colorR == 0 ||circle.colorR == 255)
            //     delta.dColorR = -delta.dColorR;
            // if(circle.colorG == 0 ||circle.colorG == 255)
            //     delta.dColorG = -delta.dColorG;
            // if(circle.colorB == 0 ||circle.colorB == 255)
            //     delta.dColorB = -delta.dColorB;    

            // circle.colorR += delta.dColorR;
            // circle.colorG += delta.dColorG;
            // circle.colorB += delta.dColorB;

            if(!circle.alive)
            {
                circle.radius-=10;
                circle.colorA -= 0.1;
                
                if(circle.colorA < 0)
                    circle.colorA = 0;
                if(circle.radius < 0)
                    circle.radius = 0;
                circle.respawnCountdown--;
                if(circle.respawnCountdown <= 0)
                {
                    var nC = this.resetCircle(circle);
                    circle.x = nC.x;
                    circle.y = nC.y;
                    circle.alive = nC.alive;
                    circle.radius = nC.radius;
                    circle.respawnCountdown = nC.respawnCountdown;
                    circle.colorR = nC.colorR;
                    circle.colorG = nC.colorG;
                    circle.colorB = nC.colorB;
                    circle.colorA = nC.colorA;
                }
            }
            else{
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
    
                if(this.mouse.x - circle.x < this.detectRadius && this.mouse.x - circle.x > -this.detectRadius && this.mouse.y - circle.y < this.detectRadius && this.mouse.y - circle.y > -this.detectRadius)
                {
                    circle.x-=delta.dx*0.8;
                    circle.y-=delta.dy*0.8;
                    if(circle.radius < this.maxRadius)
                    { 
                        var fakeDistance = Math.max(Math.abs(this.mouse.x - circle.x) , Math.abs(this.mouse.y - circle.y));
                        var burstSpeedModifier = Math.min(((this.detectRadius- fakeDistance) / (this.detectRadius * 2))*2 ,1);
                        // console.log(burstSpeedModifier);
                        circle.radius += this.burstSpeed * burstSpeedModifier;// - (this.maxRadius - circle.radius) * 0.8;
                        if(circle.colorR <= 255)
                            circle.colorR++;
                        if(circle.colorG <= 255)
                            circle.colorG++;
                        if(circle.colorB <= 255)
                            circle.colorB++;
                            
                    }
                    else if(circle.radius >= this.maxRadius)
                    {
                        this.burstCount++;
                        circle.radius = this.burstRadius;
                        circle.colorR = 255;
                        circle.colorG = 255;
                        circle.colorB = 255;
                        circle.alive = false;
                    }
    
                }
                else{
                    if(circle.radius > this.defaultRadius)
                        circle.radius -= 0.5;
                }
    
                if(this.mouseDown){
                    if(this.detectRadius <= this.maxDetectRadius)
                        this.detectRadius += 0.005;
                }
                else{
                    if(this.detectRadius >= this.defaultDetectRadius)
                        this.detectRadius -= 0.005;
                }
            }
            
        },

        updatePOW:function(pow){            
            if(pow.respawnCountdown <= 0)
            {
                pow.alive =true;
                pow.respawnCountdown = this.powerUPCoolDown;
            }
            pow.respawnCountdown--;
        },


        animate:function(){
            
            
            requestAnimationFrame(this.animate);
            this.clear();
            for(let index in this.circles)
            {
                this.drawArc(this.circles[index]);
                this.update(this.circles[index],this.deltas[index]);
                // console.log("index:" + index + " is alive?" + this.circles[index].alive)
            }
            for(let pow in this.powerUps)
            {
                this.drawArc(this.powerUps[pow]);
                this.updatePOW(this.powerUps[pow]);
            }
                this.vueCanvas.fillStyle = "white";
                this.vueCanvas.font = "30px Arial";
                this.vueCanvas.fillText(this.burstCount,10,50);
            // this.update();

        

        },



        resetCircle:function(){
            var newCircle = {
                x:0,
                y:0,
                radius:this.defaultRadius,//+Math.floor(Math.random() * 30),
                colorR:0,
                colorG:0,
                colorB:0,
                colorA:1,
                respawnCountdown:this.circleRespawnCountdown,
                alive:true
            }

            newCircle.x = Math.random() * (window.innerWidth - newCircle.radius * 2) + newCircle.radius;
            newCircle.y = Math.random() * (window.innerHeight - newCircle.radius * 2) + newCircle.radius;

            var h = 217/360;
            var s = Math.random();
            var v = 1.00;
            var hsv = this.HSVtoRGB(h,s,v);

            // console.log(hsv);

            newCircle.colorR = hsv.r
            newCircle.colorG = hsv.g
            newCircle.colorB = hsv.b

            return newCircle;
        },
        HSVtoRGB:function(h, s, v) {
            var r, g, b, i, f, p, q, t;
            if (arguments.length === 1) {
                s = h.s; v = h.v; h = h.h;
            }
            i = Math.floor(h * 6);
            f = h * 6 - i;
            p = v * (1 - s);
            q = v * (1 - f * s);
            t = v * (1 - (1 - f) * s);
            switch (i % 6) {
                case 0: r = v; g = t; b = p; break;
                case 1: r = q; g = v; b = p; break;
                case 2: r = p; g = v; b = t; break;
                case 3: r = p; g = q; b = v; break;
                case 4: r = t; g = p; b = v; break;
                case 5: r = v; g = p; b = q; break;
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