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

  {barrage: '@Xleft小叮当:你是怎么做到看起来很忙。一点用没有的？'},
  {barrage: '@XTSZzZzz：这个游戏，不只有狼，还有很多🐷'},
  {barrage: '@阿乐fps：造李蛙！造李蛙！造李蛙！造李蛙！造李蛙！造李蛙！造李蛙！造李蛙！'},
  {barrage: '@冬瓜强的正宫娘娘阿胖:每晚都说弄死我,没过两分钟都把自己弄成死鱼'},
  {barrage: '@冬瓜强的正宫娘娘啊胖：如果你再不戒烟，那个B橡胶圈你就带一辈子！'},
  {barrage: '@精神永富:这些片总只是第一波嗷瓜强，别觉得踢了我能好过。'},
  {barrage: '@馒头:冬瓜强你大主播，你了不起，你清高，你就打压我吧，我就是要一步一步一步一步爬到最高'},
  {barrage: '@娆春乔:冬瓜，去！给我叼回来！🦴🦴🦴🦴🦴'},
  {barrage: '@吴尚泽123：强弟，我是不是给你脸了，明天就叫我爸爸打压你'},
  {barrage: '@吴尚泽123:强叔，你真fw'},
  {barrage: '@杨一歌 ： 游戏看不懂，帅脸看不到，就知道抽抽抽，死了得了人夫哥'},
  {barrage: '@钻粉十级牌进群私信我：冬瓜强我是不是给你脸了？再骂一句试试？'},
  {barrage: '《刘迪更新了》《明天就刘迪》《等刘迪结束就刘迪》《再刷刘迪杀了》'},
  {barrage: '꧁✨❤瑞斌瑞斌，直击我心❤✨꧂꧁✨❤瑞斌瑞斌，直击我心❤✨꧂꧁✨❤瑞斌瑞斌，直击我心❤✨꧂'},
  {barrage: 'Newhappy明明：强哥战地还玩吗？不玩我退款了，买了一次都不叫我玩'},
  {barrage: '🐱🎈：冬瓜别玩了,我在后室里等你了。'},
  {barrage: '🐱真的要娶馨儿么'},
  {barrage: '💖꧁麒芳麒芳，举世无双꧂💖💖꧁麒芳麒芳，举世无双꧂💖💖꧁麒芳麒芳，举世无双꧂'},
  {barrage: '阿胖越胖，吃的越多，吃的越多，胃里空间越少，空间越少，吃的越少，阿胖越瘦，所以阿胖越胖，阿胖越瘦'},
  {barrage: '超哥😭超哥😭超哥😭超哥😭超哥😭超哥😭超哥😭超哥😭超哥😭超哥😭超哥😭超哥😭'},
  {barrage: '诶 你身上有炸弹哦！———Wegame世一吉格斯 绿色贝雷蛙 一键天赋已配备'},
  {barrage: '很难想象同样是尤物，猫就蠢的很幽默，冬瓜就蠢的很想骂，这是为什么呢？'},
  {barrage: '很喜欢老白的一句话 冬瓜强的残局过程很精彩 结局却往往不尽人意'},
  {barrage: '绝食鹈鹕，永生加拿大，无爪猎鹰，炸膛刺客，小脑警长，那你无敌了呀🤡'},
  {barrage: '扣1复活钙奶扣1复活钙奶扣1复活钙奶扣1复活钙奶扣1复活钙奶'},
  {barrage: '馒头平时打得多好，发多少枪，多么奉献。只要在冬瓜眼里失误一次，那就要被说一晚上'},
  {barrage: '没！有！红！烧！贝！雷！蛙！我！就！要！闹！'},
  {barrage: '没！有！京！✌🏻！我！就！要！闹！'},
  {barrage: '麒芳😭麒芳😭麒芳😭麒芳😭麒芳😭麒芳😭麒芳😭麒芳😭麒芳😭麒芳😭麒芳😭麒芳'},
  {barrage: '我是来自浙江余姚市的宝宝。名字叫叶枝高，曾效力于New4 Nface 拿过许许多多不同的冠军'},
  {barrage: '小将😭钙奶😭麒芳😭藏原走😭小葡萄😭橘子可😭龙😭明神😭左神😭'},
  {barrage: '小团体一个京✌🏻八套房，一个云南大象王，就你是个余姚讲述人'},
  {barrage: '馨儿真的要嫁给🐱吗'},
  {barrage: '叶枝高，出了名了不回老同学消息了，明天我们中专模具班聚会你来不来，有董小倩'},
  {barrage: '泳裤骂过娘，刘頔发过狂，小鬼流过氓，茄子称过王，苏弟破过房，超哥刀过狼，冬瓜吃过翔'},
  {barrage: '有！绿！色！贝！雷！蛙！那！我！就！不！闹！了！'},
  {barrage: '大刘頔啦，漂亮刘頔娶了，大刘頔住上了，C刘頔开上了，嫌刘頔们烦了'},
  {barrage: '我接受大家的批评同时也给大家一个直接明确的交代，我现在的状态是：刘頔'},
  {barrage: '袁瑞斌就是那种会哭着打奶嗝，眼睛会泛着水光的小奶狗一枚呀，软软的，浑身奶香'},
  {barrage: '《我要刘迪了》《直接刘迪了》《刘迪两天》《忘了 刘迪两口》《闻闻刘迪》《不管了刘了迪了》'},
  {barrage: '一天刘顿被刘迪砸到了脑袋，从而发现了刘迪力学'},
  {barrage: '解说队伍：激情泳裤，呐喊茄子，专业卡萨，热情钙奶，哎哟馒头，完了刘迪，读秒冬瓜'},
  {barrage: '瑞斌♥瑞斌♥直击我心♥'},
  {barrage: '京✌就像公园里一件事能叨逼叨一天的大爷，冬瓜就厉害了，是大爷不牵绳的狗'},
  {barrage: '不怕渣女玩暧昧，就怕瑞斌三十岁，识大体 懂进退 撒娇卖萌全都会 解风情有韵味让人迷恋又沉醉'},
  {barrage: '我在“争夺余姚公主”活动中，使用了角色袁瑞斌，最终不敌洛宝，被瓜强关进地下室。你也来试试吧'},
  {barrage: '不是，我骂你几句怎么啦？你还不愿意听了？不是为你好？我咋不骂苏弟呢？'},
  {barrage: '叶先生您好，您购买的真人比例馒头90kg【私密发货】【新型硅胶】已经到达驿站，请及时取走'},
  {barrage: '你说的这些我都不懂 我就知道有个叫白坤华的自从那场nuke后 连人带连狙消失了'},
  {barrage: '"冬瓜强不要！那里太烫了！"不顾阿胖的惊恐哭泣，冬瓜强狞笑着把头伸进了烤箱变成了猪头"'},
  {barrage: '刘頔越大，刘頔越小，刘越大，所以頔越小，頔越小，所以刘越大，所以刘頔越大，刘頔越小'},
  {barrage: '麒芳麒芳麒芳麒芳麒芳麒芳麒芳麒芳麒芳麒芳'},



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