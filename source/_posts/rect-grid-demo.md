---
title: 实例demo之3D transform立方体效果
date: 2016-07-24T17:41:57.000Z
categories: 工作
tags:
  - demo
  - CSS3
toc: false
---

--------------------------------------------------------------------------------

<style>
    @keyframes rotate-frame {
        0% {
            transform: rotateX(0deg) rotateY(0deg);
        }
        10% {
            transform: rotateX(0deg) rotateY(180deg);
        }
        20% {
            transform: rotateX(-180deg) rotateY(180deg);
        }
        30% {
            transform: rotateX(-360deg) rotateY(180deg);
        }
        40% {
            transform: rotateX(-360deg) rotateY(360deg);
        }
        50% {
            transform: rotateX(-180deg) rotateY(360deg);
        }
        60% {
            transform: rotateX(90deg) rotateY(180deg);
        }
        70% {
            transform: rotateX(0) rotateY(180deg);
        }
        80% {
            transform: rotateX(90deg) rotateY(90deg);
        }
        90% {
            transform: rotateX(90deg) rotateY(0);
        }
        100% {
            transform: rotateX(0) rotateY(0);
        }
    }

    .my-container {
        width: 62.5rem;
        height: 31.25rem;
        margin: 0 auto;
        transform: scale(0.6) translateY(-10rem);
    }

    .rect-wrap {
        position: relative;
        perspective: 100rem;
    }

    .container {
        width: 50rem;
        height: 50rem;
        transform-style: preserve-3d;
        transform-origin: 50% 50% 12.5rem;
        animation: rotate-frame 30s linear infinite;
    }

    .slide {
        width: 25rem;
        height: 25rem;
        position: absolute;
        box-shadow: 0 0 1.25rem rgba(0, 0, 0, 0.9) inset;
    }

    .top {
        left: 12.5rem;
        top: -12.5rem;
        transform: rotateX(-90deg);
        transform-origin: bottom;
        background: url(http://7xtawy.com1.z0.glb.clouddn.com/img1.jpeg) no-repeat;
        background-size: 100% 100%;
    }

    .bottom {
        left: 12.5rem;
        bottom: -12.5rem;
        transform: rotateX(90deg);
        transform-origin: top;
        background: url(http://7xtawy.com1.z0.glb.clouddn.com/img2.jpeg) no-repeat;
        background-size: 100% 100%;
    }

    .left {
        left: -12.5rem;
        top: 12.5rem;
        transform: rotateY(90deg);
        transform-origin: right;
        background: url(http://7xtawy.com1.z0.glb.clouddn.com/img3.jpeg) no-repeat;
        background-size: 100% 100%;
    }

    .right {
        left: 37.5rem;
        top: 12.5rem;
        transform: rotateY(-90deg);
        transform-origin: left;
        background: url(http://7xtawy.com1.z0.glb.clouddn.com/img4.jpeg) no-repeat;
        background-size: 100% 100%;
    }

    .front {
        left: 12.5rem;
        top: 12.5rem;
        transform: translateZ(25rem);
        background: url(http://7xtawy.com1.z0.glb.clouddn.com/img5.jpeg) no-repeat;
        background-size: 100% 100%;
    }

    .back {
        left: 12.5rem;
        top: 12.5rem;
        transform: translateZ(0);
        background: url(http://7xtawy.com1.z0.glb.clouddn.com/img6.jpeg) no-repeat;
        background-size: 100% 100%;
    }
</style>


<div class="my-container"><div class="rect-wrap"><div class="container">
    <div class="top slide"></div>
    <div class="bottom slide"></div>
    <div class="left slide"></div>
    <div class="right slide"></div>
    <div class="front slide"></div>
    <div class="back slide"></div>
</div></div></div>

--------------------------------------------------------------------------------
