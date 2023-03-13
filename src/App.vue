<template></template>

<script  setup>
import * as PIXI from 'pixi.js'



// 创建应用
const app = new PIXI.Application({
  width: window.innerWidth,
  height: window.innerHeight - 5,
  backgroundColor: 'white',
  resolution: window.devicePixelRatio | 1
})
document.body.append(app.view)

const container = new PIXI.Container()
app.stage.addChild(container)

const bgBaseTexture = PIXI.BaseTexture.from('./茶杯赛跑者/10007.png')

const bgTexture = new PIXI.Texture(bgBaseTexture, new PIXI.Rectangle(1024, 1024, 866, 365))
const bgSprite = new PIXI.TilingSprite(bgTexture)
bgSprite.width = 866
bgSprite.height = 365
bgSprite.scale.set(app.screen.height / bgSprite.height)
container.addChild(bgSprite)

const barrierTexture = new PIXI.Texture(bgBaseTexture, new PIXI.Rectangle(1534, 1560, 356, 400))
const barrierSprite = new PIXI.Sprite(barrierTexture)
barrierSprite.scale.set(0.5)
barrierSprite.x = app.screen.width - barrierSprite.width
barrierSprite.y = app.screen.height - barrierSprite.height
container.addChild(barrierSprite)

const roleTexture = new PIXI.Texture(bgBaseTexture, new PIXI.Rectangle(0, 0, 1000, 1260))
const roleSprite = new PIXI.Sprite(roleTexture)
roleSprite.scale.set(0.2)
container.addChild(roleSprite)

const runBaseTexture = PIXI.BaseTexture.from('./茶杯赛跑者/10010.png')
const runTexture = []
for (let i = 0; i < 2; i++) {
  for (let j = 1; j <= 5; j++) {
    runTexture.push(new PIXI.Texture(
      runBaseTexture,
      new PIXI.Rectangle(119 + 138 * j, 153 * i, 137, 153)
    ))
  }
}
const runSprite = new PIXI.AnimatedSprite(runTexture)
runSprite.animationSpeed = 0.1
runSprite.scale.set(0.9)
runSprite.x = 100
runSprite.y = 680 - runSprite.height
runSprite.visible = false
container.addChild(runSprite)



const goldBaseTexture = PIXI.BaseTexture.from('./茶杯赛跑者/10004.png')
const goldTexture = []
for (let i = 0; i < 4; i++) {
  for (let j = 0; j < 4; j++) {
    goldTexture.push(new PIXI.Texture(
      goldBaseTexture,
      new PIXI.Rectangle(240 * j, 30 + 510 * i, 240, 269)
    ))
  }
}


const randomPosition = (Sprite) => {
  const i = Math.floor(Math.random() * 3)
  const pos = 670 - Sprite.height - i * 160
  Sprite.x = app.screen.width + 20 + Math.random() * (goldSprite.width + barrierSprite.width - goldSprite.width / 2) + goldSprite.width / 2
  Sprite.y = pos
}

const goldSprite = new PIXI.AnimatedSprite(goldTexture)
goldSprite.animationSpeed = 0.2
goldSprite.scale.set(0.3)
randomPosition(goldSprite)
goldSprite.visible = false
container.addChild(goldSprite)

let isGameBegin = false
let isGameOver = false

const gameBegin = () => {
  window.onkeydown = (e) => {
    if (e.which === 40 && runSprite.y < 517) {
      runSprite.y += 165
    } else if (e.which === 38 && runSprite.y > 217) {
      runSprite.y -= 165
    }
  }
  roleSprite.visible = false
  isGameBegin = true
  startSprit.visible = false
  startBtnSprit.visible = false
  barrierSprite.scale.set(0.2)
  randomPosition(barrierSprite)
  runSprite.visible = true
  runSprite.play()
  goldSprite.visible = true
  goldSprite.play()
}

const infoBaseTexture = PIXI.BaseTexture.from('./茶杯赛跑者/10006.png')

const starTexture = new PIXI.Texture(infoBaseTexture, new PIXI.Rectangle(30, 260, 650, 206))
const startSprit = new PIXI.Sprite(starTexture)
startSprit.x = (app.screen.width - startSprit.width) / 2
startSprit.y = (app.screen.height - startSprit.height) / 2
container.addChild(startSprit)

const startBtnTexture = new PIXI.Texture(infoBaseTexture, new PIXI.Rectangle(764, 0, 233, 292))
const startBtnSprit = new PIXI.Sprite(startBtnTexture)
startBtnSprit.scale.set(0.4)
startBtnSprit.x = (app.screen.width - startBtnSprit.width) / 2.5
startBtnSprit.y = (app.screen.height - startBtnSprit.height) / 1.5
startBtnSprit.interactive = true
startBtnSprit.on('click', gameBegin)
container.addChild(startBtnSprit)

const endTexture = new PIXI.Texture(infoBaseTexture, new PIXI.Rectangle(0, 512, 410, 320))
const endSprite = new PIXI.Sprite(endTexture)
endSprite.scale.set(0.7)
endSprite.x = (app.screen.width - endSprite.width) / 2
endSprite.y = -endSprite.height
container.addChild(endSprite)

let score = 0
const gameOver = () => {
  goldSprite.visible = false
  runSprite.visible = false
  app.ticker.add(() => {
    if (endSprite.y < (app.screen.height - endSprite.height) / 2) {
      endSprite.y += 2
    }
  })

}
app.ticker.add(() => {
  if (isGameBegin) {
    if (Math.abs(goldSprite.x - barrierSprite.x) < Math.min(goldSprite.width, barrierSprite.width)) {
      randomPosition(goldSprite)
      randomPosition(barrierSprite)
    }
    bgSprite.tilePosition.x -= 3
    goldSprite.x -= 3
    barrierSprite.x -= 3
    if (goldSprite.x < -30) {
      randomPosition(goldSprite)
    }
    if (Math.abs(runSprite.x - goldSprite.x) < runSprite.width && Math.abs(runSprite.y - goldSprite.y) < runSprite.height) {
      randomPosition(goldSprite)
      score++;
    }
    if (barrierSprite.x < -30) {
      randomPosition(barrierSprite)
    }
    if (Math.abs(runSprite.x - barrierSprite.x) < barrierSprite.width && Math.abs(runSprite.y - barrierSprite.y) < barrierSprite.height) {
      isGameBegin = false
      isGameOver = true
    }
  }
  if (isGameOver) gameOver()

})


</script>

<style scoped lang='scss'></style>