<template>
  <div class="plotly">
    <div class="input-data">
        <div>
            <p>r00xre<input v-model="r00xre"></p>
            <p>r10xre<input v-model="r10xre"></p>
            <p>r20xre<input v-model="r20xre"></p>
            <p>r30xre<input v-model="r30xre"></p>
            <p>r00yre<input v-model="r00yre"></p>
        </div>
        <div>
            <p>r10yre<input v-model="r10yre"></p>
            <p>r20yre<input v-model="r20yre"></p>
            <p>r30yre<input v-model="r30yre"></p>
            <p>r00xim<input v-model="r00xim"></p>
            <p>r00yim<input v-model="r00yim"></p>
        </div>
        <button @click="buildPlot">Build</button>
    </div>

    <div  id="plotlyDiv"></div>
  </div>
</template>

<script>
    let math = require('mathjs');
export default {

  name: 'Craph',
    data() {
        return {
            dataPlot: [],
            layout : {},
            r00xre: 1.0,
            r10xre:4.0,
            r20xre:3.0,
            r30xre:6.0,
            r00yre:1.0,
            r10yre:2.0,
            r20yre:4.0,
            r30yre:5.0,
            r00xim:2.0,
            r00yim:2.0,
        }
    },
    mounted() {
        this.buildPlot();
    },
    methods: {
        buildPlot() {

            let i = math.complex('i');

            let AX=math.matrix([
                [math.complex('1+i'),math.complex('4+3i'),math.complex('3+3i'),math.complex('5+6i')],
                [math.complex('2+3i'),math.complex('3+4i'),math.complex('4+5i'),math.complex('5+6i')],
                [math.complex('3+4i'),math.complex('4+5i'),math.complex('5+6i'),math.complex('6+7i')],
                [math.complex('4+5i'),math.complex('6+7i'),math.complex('6+9i'),math.complex('7+10i')]
            ]);

            let AY=math.matrix([
                [math.complex('1+4i'),0,0,0],
                [math.complex('3+3i'),0,0,0],
                [math.complex('2+2i'),0,0,0],
                [math.complex('4+i'),0,0,0]
            ]);

            let AZ=math.matrix([
                [0,0,0,0],
                [0,0,0,0],
                [0,0,0,0],
                [0,0,0,0]
            ]);

            let r00xre = this.r00xre;
            let r10xre = this.r10xre;
            let r20xre = this.r20xre;
            let r30xre = this.r30xre;
            let r00yre = this.r00yre;
            let r10yre = this.r10yre;
            let r20yre = this.r20yre;
            let r30yre = this.r30yre;
            let r00z = 0;
            let r10z = 0;
            let r20z = 0;
            let r30z = 0;
            let r00xim = this.r00xim;
            let r00yim = this.r00yim;

            let r20xim = -r00yre+r20yre+r00xim;
            let r20yim = r00xre-r20xre+r00yim;
            let r10xim = 1/2*(-r00yre+2*r10yre-r20yre+r00xim+r20xim);
            let r10yim = 1/2*(r00xre-2*r10xre+r20xre+r00yim+r20yim);
            let r30yim = r00yim-3*r10yim+3*r20yim+r00xre-3*r10xre+3*r20xre-r30xre;
            let r30xim = -r00yre+3*r10yre-3*r20yre+r30yre+r00xim-3*r10xim+3*r20xim;

            let r00x = math.add(r00xre,math.multiply(i,r00xim));
            let r10x = math.add(r10xre,math.multiply(i,r10xim));
            let r20x = math.add(r20xre,math.multiply(i,r20xim));
            let r30x = math.add(r30xre,math.multiply(i,r30xim));
            let r00y = math.add(r00yre,math.multiply(i,r00yim));
            let r10y = math.add(r10yre,math.multiply(i,r10yim));
            let r20y = math.add(r20yre,math.multiply(i,r20yim));
            let r30y = math.add(r30yre,math.multiply(i,r30yim));




            AX[1, 1] = r00x;
            AX[1, 2] = r10x;
            AX[1, 3] = r20x;
            AX[1, 4] = r30x;
            AY[1, 1] = r00y;
            AY[1, 2] = r10y;
            AY[1, 3] = r20y;
            AY[1, 4] = r30y;

            console.log(AX);

            //Формирование  координат вершин характеристического многоугольника и доведение их с 4 ти до 8 ми путем кратных конечных
            //вершин(три первых и три последних вершины кратные)

            let stroka=1;

            let AXCurv = [AX[1],AX[2],AX[3],AX[4]];
            let AYCurv = [AY[1],AY[2],AY[3],AY[4]];

            //console.log(AXCurv,AYCurv)

            let AZCurv = AZ._data[1];


            let AXCurvPeriod = [AXCurv[0], AXCurv[0], AXCurv[0], AXCurv[1], AXCurv[2], AXCurv[3],AXCurv[3],AXCurv[3]];
            let AYCurvPeriod = [AYCurv[0], AYCurv[0], AYCurv[0], AYCurv[1], AYCurv[2], AYCurv[3],AYCurv[3],AYCurv[3]];
            let AZCurvPeriod = [AZCurv[0], AZCurv[0], AZCurv[0], AZCurv[1], AZCurv[2], AZCurv[3],AZCurv[3],AZCurv[3]];



            let Xline=[];
            const one_six=1/6;
            let const_array=math.matrix([
                [1,3,-3,1],
                [3,-6,3,0],
                [-3,0,3,0],
                [1,4,1,0]
            ]);
            let tvector=[0,math.add(0.1,math.multiply(i,0.1)),math.add(0.2,math.multiply(i,0.2)),math.add(0.3,math.multiply(i,0.3)),
                math.add(0.4,math.multiply(i,0.4)),math.add(0.5,math.multiply(i,0.5)),math.add(0.6,math.multiply(i,0.6)),math.add(0.7,math.multiply(i,0.7)),
                math.add(0.8,math.multiply(i,0.8)),math.add(0.9,math.multiply(i,0.9)),math.add(1,math.multiply(i,1)) ];

            for (let j=0; j<5; j++)
            {
                for (let k=0; k<5; k++)
                {
                    for (let u=0; u<1; u=u+0.01 ){

                        let t=math.re(tvector[k]);
                        let array_t=[math.pow(t,3), math.pow(t,2), t, 1];
                        let CurvPeriodX=[AXCurvPeriod[j],AXCurvPeriod[j+1],AXCurvPeriod[j+2],AXCurvPeriod[j+3]];
                        let CurvPeriodY=[AYCurvPeriod[j],AYCurvPeriod[j+1],AYCurvPeriod[j+2],AYCurvPeriod[j+3]];
                        let CurvPeriodZ=[AZCurvPeriod[j],AZCurvPeriod[j+1],AZCurvPeriod[j+2],AZCurvPeriod[j+3]];

                        let X1=math.re(math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),CurvPeriodX));
                        let Y1=math.re(math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),CurvPeriodY));
                        let Z1=math.re(math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),CurvPeriodZ));
                        let tt=math.re(tvector[k+1]);
                        let array_tt=[math.pow(tt,3), math.pow(tt,2), tt, 1];
                        let X2=math.re(math.multiply(math.multiply(math.multiply(array_tt,one_six),const_array),CurvPeriodX));
                        let Y2=math.re(math.multiply(math.multiply(math.multiply(array_tt,one_six),const_array),CurvPeriodY));
                        let Z2=math.re(math.multiply(math.multiply(math.multiply(array_tt,one_six),const_array),CurvPeriodZ));
                        Xline.push({
                            XX: math.add(math.multiply(math.subtract(X2,X1),u),X1),
                            YY: math.add(math.multiply(math.subtract(Y2,Y1),u),Y1),
                            ZZ: math.add(math.multiply(math.subtract(Z2,Z1),u),Z1)
                        });
                    }
                }
            }

            let kkline = [];

            for (let j = 0; j < 3; j++) {
                for (let v = 0; v <= 1; v += .01) {
                    kkline.push({
                        XX: math.re(math.add(math.multiply(math.subtract(AXCurv[j + 1], AXCurv[j]), v), AXCurv[j])),
                        YY: math.re(math.add(math.multiply(math.subtract(AYCurv[j + 1], AYCurv[j]), v), AYCurv[j])),
                        ZZ: math.re(math.add(math.multiply(math.subtract(AZCurv[j + 1], AZCurv[j]), v), AZCurv[j]))
                    });
                }
            }

            let plot1 = [];
            let plot2 = [];
            let plot3 = [];
            let plot4 = [];
            let plot5 = [];
            let xk_array=[AXCurvPeriod[0],AXCurvPeriod[1],AXCurvPeriod[2],AXCurvPeriod[3]];
            let yk_array=[AYCurvPeriod[0],AYCurvPeriod[1],AYCurvPeriod[2],AYCurvPeriod[3]];
            let xk_array2=[AXCurvPeriod[1],AXCurvPeriod[2],AXCurvPeriod[3],AXCurvPeriod[4]];
            let yk_array2=[AYCurvPeriod[1],AYCurvPeriod[2],AYCurvPeriod[3],AYCurvPeriod[4]];
            let xk_array3=[AXCurvPeriod[2],AXCurvPeriod[3],AXCurvPeriod[4],AXCurvPeriod[5]];
            let yk_array3=[AYCurvPeriod[2],AYCurvPeriod[3],AYCurvPeriod[4],AYCurvPeriod[5]];
            let xk_array4=[AXCurvPeriod[3],AXCurvPeriod[4],AXCurvPeriod[5],AXCurvPeriod[6]];
            let yk_array4=[AYCurvPeriod[3],AYCurvPeriod[4],AYCurvPeriod[5],AYCurvPeriod[6]];
            let xk_array5=[AXCurvPeriod[4],AXCurvPeriod[5],AXCurvPeriod[6],AXCurvPeriod[7]];
            let yk_array5=[AYCurvPeriod[4],AYCurvPeriod[5],AYCurvPeriod[6],AYCurvPeriod[7]];

            let flagPlot=false;

            for (let u = 0; u < 1; u += 0.1) {
                let templatePlot=[];
                flagPlot=!flagPlot;
                for (let v = 0; v < 1; v += 0.1) {
                    let t = math.add(u,math.multiply(i,v));
                    let array_t=[math.pow(t,3), math.pow(t,2), t, 1];

                    let xk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),xk_array);
                    let yk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),yk_array);
                    let zk=0;

                    templatePlot.push({
                        XX: math.re(xk),
                        YY: math.re(yk),
                        ZZ: math.re(zk)
                    });

                }
                if (flagPlot)
                    plot1.push(...templatePlot);
                else
                    plot1.push(...templatePlot.reverse());
            }

            for (let u = 0; u <= 1; u += 0.1) {
                let templatePlot=[];
                flagPlot=!flagPlot;
                for (let v = 0; v <= 1; v += 0.1) {
                    let t = math.add(u,math.multiply(i,v));
                    let array_t=[math.pow(t,3), math.pow(t,2), t, 1];

                    let xk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),xk_array2);
                    let yk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),yk_array2);
                    let zk=0;

                    templatePlot.push({
                        XX: math.re(xk),
                        YY: math.re(yk),
                        ZZ: math.re(zk)
                    });
                }
                if (flagPlot)
                    plot2.push(...templatePlot);
                else
                    plot2.push(...templatePlot.reverse());
            }

            for (let u = 0; u <= 1; u += 0.1) {
                let templatePlot=[];
                flagPlot=!flagPlot;
                for (let v = 0; v <= 1; v += 0.1) {
                    let t = math.add(u,math.multiply(i,v));
                    let array_t=[math.pow(t,3), math.pow(t,2), t, 1];

                    let xk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),xk_array3);
                    let yk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),yk_array3);
                    let zk=0;

                    templatePlot.push({
                        XX: math.re(xk),
                        YY: math.re(yk),
                        ZZ: math.re(zk)
                    });
                }
                if (flagPlot)
                    plot3.push(...templatePlot);
                else
                    plot3.push(...templatePlot.reverse());
            };

            for (let u = 0; u <= 1; u += 0.1) {
                let templatePlot=[];
                flagPlot=!flagPlot;
                for (let v = 0; v <= 1; v += 0.1) {
                    let t = math.add(u,math.multiply(i,v));
                    let array_t=[math.pow(t,3), math.pow(t,2), t, 1];

                    let xk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),xk_array4);
                    let yk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),yk_array4);
                    let zk=0;

                    templatePlot.push({
                        XX: math.re(xk),
                        YY: math.re(yk),
                        ZZ: math.re(zk)
                    });
                }
                if (flagPlot)
                    plot4.push(...templatePlot);
                else
                    plot4.push(...templatePlot.reverse());
            }

            for (let u = 0; u <= 1; u += 0.1) {
                let templatePlot=[];
                flagPlot=!flagPlot;
                for (let v = 0; v <= 0.5; v += 0.1) {
                    let t = math.add(u,math.multiply(i,v));
                    let array_t=[math.pow(t,3), math.pow(t,2), t, 1];

                    let xk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),xk_array5);
                    let yk=math.multiply(math.multiply(math.multiply(array_t,one_six),const_array),yk_array5);
                    let zk=0;

                    templatePlot.push({
                        XX: math.re(xk),
                        YY: math.re(yk),
                        ZZ: math.re(zk)
                    });
                }
                if (flagPlot)
                    plot5.push(...templatePlot);
                else
                    plot5.push(...templatePlot.reverse());

            }

            console.log(plot1);

            let trace1 = {
                x: kkline.map((el) => { return el.XX }),
                y: kkline.map((el) => { return el.YY }),
                z: kkline.map((el) => { return el.ZZ }),
                line: {
                    color: 'blue',
                    width: 5
                },
                marker: {
                    color: 'blue',
                    line: {
                        color: 'blue',
                        width: 3
                    },
                    size: 12,
                    symbol: 'circle'
                },
                mode: 'lines',
                name: 'Вершини В-сплайну',
                type: 'scatter3d'
            };

            let trace2 = {
                x: plot1.map((el) => { return el.XX }),
                y: plot1.map((el) => { return el.YY }),
                z: plot1.map((el) => { return el.ZZ }),
                line: {
                    color: 'grey',
                    width: 1
                },
                marker: {
                    color: 'grey',
                    line: {
                        color: 'grey',
                        width: 1
                    },
                    size: 1,
                    symbol: 'circle'
                },
                name: 'Plot1',
                type: 'scatter3d'
            };

            let trace3 = {
                x: plot2.map((el) => { return el.XX }),
                y: plot2.map((el) => { return el.YY }),
                z: plot2.map((el) => { return el.ZZ }),
                line: {
                    color: 'grey',
                    width: 1
                },
                marker: {
                    color: 'grey',
                    line: {
                        color: 'grey',
                        width: 1
                    },
                    size: 1,
                    symbol: 'circle'
                },
                name: 'Plot2',
                type: 'scatter3d'
            };

            let trace4 = {
                x: plot3.map((el) => { return el.XX }),
                y: plot3.map((el) => { return el.YY }),
                z: plot3.map((el) => { return el.ZZ }),
                line: {
                    color: 'grey',
                    width: 1
                },
                marker: {
                    color: 'grey',
                    line: {
                        color: 'grey',
                        width: 1
                    },
                    size: 1,
                    symbol: 'circle'
                },
                name: 'Plot3',
                type: 'scatter3d'
            };

            let trace5 = {
                x: plot4.map((el) => { return el.XX }),
                y: plot4.map((el) => { return el.YY }),
                z: plot4.map((el) => { return el.ZZ }),
                line: {
                    color: 'grey',
                    width: 1
                },
                marker: {
                    color: 'grey',
                    line: {
                        color: 'grey',
                        width: 1
                    },
                    size: 1,
                    symbol: 'circle'
                },
                name: 'Plot4',
                type: 'scatter3d'
            };

            let trace6 = {
                x: plot5.map((el) => { return el.XX }),
                y: plot5.map((el) => { return el.YY }),
                z: plot5.map((el) => { return el.ZZ }),
                line: {
                    color: 'grey',
                    width: 1
                },
                marker: {
                    color: 'grey',
                    line: {
                        color: 'grey',
                        width: 1
                    },
                    size: 1,
                    symbol: 'circle'
                },
                name: 'Plot5',
                type: 'scatter3d'
            };

            let trace7 = {
                x: Xline.map((el) => { return el.XX }),
                y: Xline.map((el) => { return el.YY }),
                z: Xline.map((el) => { return el.ZZ }),
                line: {
                    color: 'red',
                    width: 5
                },
                marker: {
                    color: 'red',
                    line: {
                        color: 'red',
                        width: 3
                    },
                    size: 12,
                    symbol: 'circle'
                },
                mode: 'lines',
                name: 'Ізотропний періодичний  B-сплайн 4',
                type: 'scatter3d'
            };

            this.dataPlot = [trace1, trace2,trace3, trace4, trace5,trace6, trace7];

            this.layout = {
                height: 600,
                scene: {
                    xaxis: {
                        title: 'X',
                        titlefont: { color: 'black' }
                    },
                    yaxis: {
                        title: 'Y',
                        titlefont: { color: 'black' }
                    },
                    zaxis: {
                        title: 'Z',
                        titlefont: { color: 'black' }
                    }
                },
                title: 'Line3D',
                width: 900
            };

            Plotly.newPlot('plotlyDiv',this.dataPlot,this.layout);



        }
    }

}
</script>


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

.input-data {
        display: flex;
        width: 60%;
        flex-wrap: wrap;
}

.input-data p {
    margin-left: 20px;
}


.input-data div {
    width: 50%;
}

input {
        border-radius: 3px;
        padding: 5px 10px;
        border: 1px solid #d3d3d3;
}

.plotly {
        text-align: left;
    display: flex;
    align-items: flex-start;
}

button {
    padding: 10px 30px;
    border: 1px solid ;
    border-radius: 3px;
    background-color: #fff;
    color: #000;
}
</style>
