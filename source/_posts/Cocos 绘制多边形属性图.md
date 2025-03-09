---
title: Cocos Creator 绘制多边形属性图
tags:
  - Cocos Creator
  - UI
categories:
  - Cocos Creator
  - UI
abbrlink: 39073
date: 2023-08-08 14:31:30
---
### 具体代码如下：
```
import { _decorator, Color, Component, Graphics, Vec2 } from 'cc';
const { ccclass, property } = _decorator;

export interface RadarData {
    values: number[];
    lineWidth?: number;
    lineColor?: Color;
    fillColor?: Color;
    joinColor?: Color;
}

@ccclass('RadarMap')
export class RadarMap extends Component {

    private _graphics: Graphics;

    @property
    offsetAngle: number = 90;

    @property
    propertyCount: number = 5;

    @property
    scaleCount: number = 3;

    @property
    scaleLength: number = 250;

    @property
    normalLineWidth: number = 4;

    @property(Color)
    normalLineColor: Color = new Color('#3D8BFF');

    @property(Color)
    normalFillColor: Color = new Color('#3D8BFF66');

    @property(Color)
    normalJoinColor: Color = new Color('#FFD633');

    @property
    bgLineWidth = 3;

    @property(Color)
    bgStrokeColor = new Color('#C3C3C3');

    @property(Color)
    bgFillColor = new Color('#666666');

    private angles: number[] = [];

    start() {
        this._graphics = this.getComponent(Graphics);
    }

    test() {
        this.DrawBg();
        this.DrawData([
            { values: [1, 0.8, 0.6, 0.4, 0.2], lineColor: new Color('#3D8BFF'), fillColor: new Color('#3D8BFF66'), joinColor: new Color('#FFD633') },
            // { values: [0.8, 0.4, 0.3, 1, 0.4], lineColor: new Color('#FF9E52'), fillColor: new Color('#FF9E5266'), joinColor: new Color('#FFD633') },
            // { values: [0.4, 1, 0.6, 0.2, 0.8] },
        ])
    }

    DrawBg(): void {
        this._graphics.clear();
        this._graphics.lineWidth = this.bgLineWidth;
        this._graphics.strokeColor = this.bgStrokeColor;
        this._graphics.fillColor = this.bgFillColor;

        const angle = 360 / this.propertyCount;
        this.angles = [];

        for (let i = 0; i < this.propertyCount; i++) {
            this.angles.push(angle * i + this.offsetAngle);
        }

        let scales: Vec2[][] = [];
        for (let i = 0; i < this.scaleCount; i++) {
            let scale: Vec2[] = [];
            const length = this.scaleLength - this.scaleLength / this.scaleCount * i;
            for (let j = 0; j < this.angles.length; j++) {
                const radian = (Math.PI / 180) * this.angles[j];
                const pos = new Vec2(length * Math.cos(radian), length * Math.sin(radian));
                scale.push(pos);
            }
            scales.push(scale);
        }

        for (let i = 0; i < scales[0].length; i++) {
            this._graphics.moveTo(0, 0);
            this._graphics.lineTo(scales[0][i].x, scales[0][i].y);
        }

        this._graphics.moveTo(scales[0][0].x, scales[0][0].y);

        for (let i = 1; i < scales[0].length; i++) {
            this._graphics.lineTo(scales[0][i].x, scales[0][i].y);
        }
        this._graphics.close();

        this._graphics.fill();
        this._graphics.stroke();

        for (let i = 1; i < scales.length; i++) {
            this._graphics.moveTo(scales[i][0].x, scales[i][0].y);
            for (let j = 1; j < scales[0].length; j++) {
                this._graphics.lineTo(scales[i][j].x, scales[i][j].y);
            }
            this._graphics.close();
        }
        this._graphics.stroke();
    }

    DrawData(data: RadarData | RadarData[]) {
        const datas = Array.isArray(data) ? data : [data];

        for (let i = 0; i < datas.length; i++) {
            this._graphics.strokeColor = datas[i].lineColor || this.normalLineColor;
            this._graphics.fillColor = datas[i].fillColor || this.normalFillColor;
            this._graphics.lineWidth = datas[i].lineWidth || this.normalLineWidth;


            let coords = [];
            for (let j = 0; j < this.angles.length; j++) {
                if (!datas[i].values[j]) {
                    break;
                }
                const value = datas[i].values[j] > 1 ? 1 : datas[i].values[j];
                const length = this.scaleLength * value;
                const radian = (Math.PI / 180) * this.angles[j];
                const pos = new Vec2(length * Math.cos(radian), length * Math.sin(radian));
                coords.push(pos);
            }

            this._graphics.moveTo(coords[0].x, coords[0].y);
            for (let j = 1; j < coords.length; j++) {
                this._graphics.lineTo(coords[j].x, coords[j].y);
            }
            this._graphics.close();
            this._graphics.fill();
            this._graphics.stroke();

            for (let j = 0; j < coords.length; j++) {
                this._graphics.strokeColor = datas[i].lineColor || this.normalLineColor;
                this._graphics.circle(coords[j].x, coords[j].y, 2);
                this._graphics.stroke();

                this._graphics.strokeColor = datas[i].joinColor || this.normalJoinColor;
                this._graphics.circle(coords[j].x, coords[j].y, .65);
                this._graphics.stroke();
            }
        }
    }
}


```