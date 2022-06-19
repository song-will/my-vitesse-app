<script setup lang="ts">
interface Item {
  isKnow: boolean
  value: string
  isMine: boolean
  mineNum: number
  x: number
  y: number
}

const RATE = 0.2

const generateData = (Width: number, Height: number): Item[][] => {
  const data = new Array(Width)
  for (let i = 0; i < data.length; i++)
    data[i] = new Array(Height)
  return data
}

const data = ref(generateData(10, 10))

const genterateMines = () => {
  for (let i = 0; i < data.value.length; i++) {
    for (let j = 0; j < data.value[i].length; j++) {
      const random = Math.random()
      const common = {
        isKnow: true,
        mineNum: -1,
        x: i,
        y: j,
      }
      data.value[i][j] = random < RATE ? { ...common, value: 'X', isMine: true } : { ...common, value: '·', isMine: false, mineNum: 0 }
    }
  }
}
genterateMines()
/**
 * 八个方向
 */
const directioins: number[][] = [
  [-1, -1],
  [0, -1],
  [1, -1],
  [-1, 0],
  [1, 0],
  [-1, 1],
  [0, 1],
  [1, 1],
]
const computeMines = () => {
  for (let i = 0; i < data.value.length; i++) {
    for (let j = 0; j < data.value[i].length; j++) {
      const temp = data.value[i][j]
      if (!temp.isMine) {
        for (let d = 0; d < directioins.length; d++) {
          const directioin = directioins[d]
          const directioinValue = data.value?.[i + directioin[0]]?.[j + directioin[1]]
          if (directioinValue && directioinValue.isMine)
            (temp.mineNum as number)++
        }
      }
    }
  }
}

const spread = (item: Item): void => {
  if (item?.mineNum === 0) {
    item.mineNum = -1
    for (let i = 0; i < directioins.length; i++) {
      const directioin = directioins[i]
      const directioinValue = data.value?.[item.x + directioin[0]]?.[item.y + directioin[1]]
      if (directioinValue && directioinValue.mineNum && directioinValue.mineNum === 0)
        directioinValue.mineNum = -1
      spread(directioinValue)
    }
  }
}

const click = (item: Item): void => {
  item.isKnow = true
  if (item.isMine)
    alert('game over')
  else
    spread(item)
}

computeMines()
</script>

<template>
  <div>
    <div i-carbon-campsite text-4xl inline-block />
    <br>
    <div inline-block border>
      <div v-for="(item, i) in data" :key="i">
        <button v-for="(subItem, j) in item" :key="j" border="~ grey/10" w="10" h="10" va-top @click="() => click(subItem)">
          {{ subItem.isMine ? 'X' : ((subItem.mineNum as number) > -1 ? subItem.mineNum : '') }}
        </button>
      </div>
    </div>
  </div>
</template>
