<template>
  <div>
    <div class="card" style="margin-bottom: 5px ">
      <el-table stripe :data="currentPageData" style="font-size: 18px;">
        <el-table-column type="index" width="60" label="序号" align="center"></el-table-column>
        <el-table-column prop="barrage" label="弹幕"></el-table-column>
        <el-table-column label="" align="center" width="85">
          <template #default="scope">
            <el-button type="primary" @click="copyText(scope.row.barrage)">复制</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="pagination-wrapper">
      <!-- 分页 -->

      <div>
        <el-pagination
            background
            layout="prev, pager, next, jumper"
            :total="tableData.length"
            :page-size="pageSize"
            @current-change="handlePageChange"
        ></el-pagination>
      </div>
      <div class="pagination-text">--------最新弹幕在后面的页号中---------</div>
    </div>
  </div>
</template>

<script setup>
import {ref, computed} from 'vue';
import {ElMessage} from 'element-plus'

//原始数据
const tableData = ref([

  {barrage: '@冬瓜强的正宫娘娘啊胖:我在“世界养猪大赛”中养出了猪王“椰汁糕冬瓜强”，点击+1'},
  {barrage: '“再见了👋，水友们😎”冬瓜强潇洒地拨动马桶上的按钮，旋转着被冲走了。'},
  {barrage: '꧁✨❤枝高枝高，冲上云霄❤✨꧂꧁✨❤枝高枝高，冲上云霄❤✨꧂꧁✨❤枝高枝高，冲上云霄❤✨꧂'},
  {barrage: '0.17ratingの伝説,5eplの赤い悪魔!執睪葉一郎とは!'},
  {barrage: 'C63的声浪盖过了兄弟们的呼声 也掩盖了你的初心'},
  {barrage: '🧡甜💛瓜💚西💙瓜💜哈🧡蜜💜瓜💚不💛爱🧡冬💛瓜❤️是💗傻🤎瓜'},
  {barrage: '爱你嘴巴嘟嘟😘爱你睡觉呼呼😴爱你撒娇哭哭😭爱你冬瓜猪猪😍'},
  {barrage: '大主播了，漂亮老婆娶了，大豪宅住上了，C63开上了，嫌兄弟们烦了'},
  {barrage: '冬瓜哥，我只是玩玩梗而已，我真不是女大学生，别私信发我那些图片了，还有，你那个挺可爱的'},
  {barrage: '端午节女大学生在纠结吃甜粽还是咸粽，冬瓜说：吃我吧，我可甜可咸，是个杂粽！'},
  {barrage: '哥谭市里你最狂🤡，马戏团里你最忙🤡，麦当劳前你站岗🤡，扑克牌里大小王🤡，斗鱼公厕冬瓜强🤡'},
  {barrage: '给主播拍照📷：🐷（正面特写）🐖（侧面特写）🐽（局部放大）🥩（过年限定）'},
  {barrage: '好无聊，牵着我的电子宠物散散步————🤡▜▜▙（冬瓜强）（可以摸）（已嘎蛋）'},
  {barrage: '开开开😡→乱踩人🤪→发言结巴😰→被投出局😭→沉默😑→开开开😡'},
  {barrage: '看到满屏幕的弹幕都在阴阳瓜瓜，心里不是滋味，只有我知道瓜瓜的努力，加油没用的东西！'},
  {barrage: '骂冬瓜可能会被禁言 但是不骂冬瓜等于一直被禁言'},
  {barrage: '你❤️✨唱💗✨的💚✨什💖✨么💜✨J💞✨B💛玩❤️✨意❤️'},
  {barrage: '你可以不攻击主播，但是等主播破防无差别攻击的时候，你别在这委屈😣'},
  {barrage: '你们在这里喷主播，就好比往菜地里泼屎，主播非但不生气只会疯狂汲取营养'},
  {barrage: '市卫生所通知：叶志高先生，您于10月2日上午进行的公益行为“捐精”检测结果为：不合格'},
  {barrage: '塔斯汀余姚店：叶枝高，你TM点不点，每次我快关门你给我来一单，我***'},
  {barrage: '卧槽哥们你装啥b装的太像了，把大伙都骗了，弹幕都在喷你是啥b，我tm以为你真是啥'},
  {barrage: '吴权清❤️叶志高🧡苏麒芳💙许昊文💛袁瑞斌💚王祎楠💜刘亦博🖤赖雪峰💖吴润波'},
  {barrage: '椰❤汁❤糕❤椰❤汁❤糕❤椰❤汁❤糕❤椰❤汁❤糕❤椰❤汁❤糕❤椰❤汁❤糕❤椰❤汁❤糕'},
  {barrage: '椰汁糕，余姚人，职业下包手，职业期间夺冠无数，2022夺得个人生涯最具含金量冠军-新冠'},
  {barrage: '叶枝高，我是李丽你还记得初三那会儿你偷我奶兜子的事吗？我昨天离婚了你现在还喜欢我吗'},
  {barrage: '叶枝高我是你高中女同桌董小倩 以前上课老掀我裙子 你还记得吗'},
  {barrage: '叶志高，我是余姚三中王龙，初中踢你进粪坑那个，最近混得不错，有空聚聚'},
  {barrage: '主播的队友真猛，像赵云一样七进七出，当然主播也很棒，在怀里没哭'},
  {barrage: '主播面对满屏的嘲讽和挖苦。他总是默默看着。从来不回应和解释。因为他明白弹幕说的都是真的'},
  {barrage: '主播这个🔴还给你，以后记得不要把鼻子掉到地上哦🤡'},
  {barrage: '又挡摄像头？你到底在自卑什么！像你这种品相的猪头肉放在哪个市场不是被抢购一空？'},
  {barrage: '我不复制我发什么？我像你一样饱读诗书满腹金轮随口就可以说出好玩的梗吗，复制都要被骂😭'},
  {barrage: '诞生于斗鱼公厕，梦想是go区一哥，喜欢喝百事的男孩，抽烟下包是他的王牌！ '},
  {barrage: '你吃东西动静小点，我家猪以为我偷喂别的猪，急的都把圈门拱破了'},
  {barrage: '《嗯》《卧槽》《牛逼》《完了》《55 54 53 52》《ok》'},
  {barrage: '0.17场上秀遇到敌人大喊救▜▜▙有点无聊就被溜‍dy63136'},
  {barrage: '白布一盖点蚊香，猛砸桌子嚼槟榔，你要问我他是谁，余姚脑瘫冬瓜强'},
  {barrage: '平日不修善果，直播折磨大伙，赢了沾沾自得，输了不能怪我，水友提刀焯水来 今日方知猪是我'},


]);

// 每页显示的数据量
const pageSize = ref(15);
// 当前页码
const currentPage = ref(1);
// 计算当前页应该显示的数据
const currentPageData = computed(() => {
  const start = (currentPage.value - 1) * pageSize.value;
  const end = start + pageSize.value;
  return tableData.value.slice(start, end);
});

// 处理页码改变的事件
const handlePageChange = (newPage) => {
  currentPage.value = newPage;
};


const open2 = () => {
  ElMessage({
    message: '复制成功',
    type: 'success',
  })
};

const open4 = () => {
  ElMessage.error('复制失败，请检查浏览器是否禁用navigator.clipboard对象或手动复制,请勿使用夸克浏览器')
};

const copyText = (text) => {
  navigator.clipboard.writeText(text)
      .then(() => {
        // 复制成功，可以显示提示信息  
        open2();
        console.log('内容已复制到剪贴板');
      })
      .catch((err) => {
        // 复制失败，可以显示错误信息  
        console.error('复制失败:', err);
        open4()
      });
};


</script>

<script>


</script>


<style scoped>
.pagination-wrapper {
  display: flex;
  align-items: center;
}

.pagination-text {
  margin-left: 50px;
  color: red;
}
</style>