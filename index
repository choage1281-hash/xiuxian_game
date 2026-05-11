<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>修仙遊戲 V1-2</title>
<style>
  body { font-family: sans-serif; padding: 10px; background:#fafafa; }
  h1 { text-align:center; }
  .actions {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
  }
  .actions button {
    margin: 5px;
    /* 在手機上讓按鈕換行佔寬50% */
    flex: 1 1 45%;
  }
  @media (min-width: 600px) {
    .actions button {
      flex: unset;
    }
  }
  .panel { border:1px solid #ccc; padding:10px; margin:10px 0; background:#fff; }
  .timer { color:#d33; }
  #inventory span { display:block; margin:4px 0; }
</style>
</head>
<body>
  <h1>修仙遊戲 V1-2</h1>
  <!-- 角色創建面板：輸入姓名與選擇性別，在開始遊戲前顯示 -->
  <div id="startPanel" class="panel" style="display:none">
    <h3>角色創建</h3>
    <label>姓名：<input type="text" id="inputName"></label><br>
    <label>性別：
      <label><input type="radio" name="gender" value="男" checked>男</label>
      <label><input type="radio" name="gender" value="女">女</label>
    </label><br>
    <button onclick="confirmStart()">開始遊戲</button>
  </div>
  <div class="panel" id="playerInfo">
    <!-- 玩家基本資料 -->
    <strong>姓名：</strong><span id="playerName">-</span><br>
    <strong>性別：</strong><span id="playerGender">-</span><br>
    <!-- 修為與經驗 -->
    <strong>境界：</strong><span id="cultivationStage">凡人一層</span><br>
    <strong>經驗值：</strong><span id="exp">0</span> / <span id="expNeeded">100</span><br>
    <!-- 可支配點數 -->
    <strong>屬性可支配點數：</strong><span id="attributePoints">0</span><br>
    <!-- 靈石 -->
    <strong>靈石：</strong><span id="spiritStones">0</span><br>
    <!-- HP/MP -->
    <strong>HP：</strong><span id="attr_hp">100</span> /
    <strong>MP：</strong><span id="attr_mp">100</span><br>
    <!-- 被動屬性 -->
    <strong>攻擊：</strong><span id="attr_attack">5</span> /
    <strong>防禦：</strong><span id="attr_defense">5</span><br>
    <strong>速度：</strong><span id="attr_speed">5</span> /
    <strong>運氣：</strong><span id="attr_luck">5</span><br>
    <!-- 主動屬性，附加分配按鈕 -->
    <strong>力量：</strong><span id="attr_strength">1</span> <button onclick="allocateAttribute('strength')">＋</button> /
    <strong>敏捷：</strong><span id="attr_dexterity">1</span> <button onclick="allocateAttribute('dexterity')">＋</button><br>
    <strong>智力：</strong><span id="attr_intelligence">1</span> <button onclick="allocateAttribute('intelligence')">＋</button> /
    <strong>體力：</strong><span id="attr_constitution">1</span> <button onclick="allocateAttribute('constitution')">＋</button><br>
    <strong>精神：</strong><span id="attr_spirit">1</span> <button onclick="allocateAttribute('spirit')">＋</button> /
    <strong>魅力：</strong><span id="attr_charisma">1</span> <button onclick="allocateAttribute('charisma')">＋</button><br>
    <!-- 修仙屬性 -->
    <strong>靈根：</strong><span id="cult_root">1</span> /
    <strong>悟性：</strong><span id="cult_comprehension">1</span><br>
    <!-- 宗門位階與狀態 -->
    <strong>仙門位階：</strong><span id="cult_sectRank">普通弟子</span><br>
    <strong>宗門狀態：</strong><span id="sectStatus">未加入</span><br>
    <!-- 裝備資訊 -->
    <strong>裝備-武器：</strong><span id="equip_weapon">無</span><br>
    <strong>裝備-盔甲：</strong><span id="equip_armor">無</span><br>
    <strong>裝備-飾品：</strong><span id="equip_accessory">無</span>
  </div>

  <div class="panel actions">
    <button onclick="startAction('train')">修練</button>
    <button onclick="startAction('mine')">採礦</button>
    <button onclick="startAction('cutWood')">砍柴</button>
    <button onclick="startAction('farm')">種田</button>
    <button onclick="startAction('hunt')">狩獵</button>
    <button onclick="showRefine('pill')">煉丹</button>
    <button onclick="showRefine('gear')">煉器</button>
    <button onclick="startAction('drawTalisman')">畫符</button>
    <button onclick="joinSect()">加入宗門</button>
    <button onclick="openShop()">商店</button>
    <button onclick="toggleRules()">遊戲說明</button>
    <button onclick="resetGame()">重置遊戲</button>
    <button onclick="exportSave()">匯出存檔</button>
    <input type="file" id="importFile" style="display:none" onchange="importSave(event)">
    <button onclick="document.getElementById('importFile').click()">匯入存檔</button>
  </div>

  <div class="panel">
    <strong>進行中：</strong><span id="currentAction">無</span>
    <span id="actionTimer" class="timer"></span>
  </div>

  <!-- 統一背包與資源顯示 -->
  <div id="inventory" class="panel">
    <h3>背包（<span id="bagCapacity">10</span>格）</h3>
    <!-- 資源列表 -->
    <div id="resourceList">
      <span id="res_stone">礦石：0</span>
      <span id="res_wood">木材：0</span>
      <span id="res_herb">靈草：0</span>
      <span id="res_meat">獸肉：0</span>
      <span id="res_elixir">丹藥：0</span>
      <span id="res_gear">裝備：0</span>
    </div>
    <!-- 背包物品列表 -->
    <ul id="bagList"></ul>
  </div>

  <!-- 遊戲說明面板，預設隱藏，可透過按鈕開啟/關閉 -->
  <div id="gameRules" class="panel" style="display:none">
    <h3>遊戲說明</h3>
    <p>本遊戲為修仙模擬。玩家可透過修練、採礦、砍柴、種田、狩獵、煉丹、煉器與畫符等行動來獲得資源和提升修為。</p>
    <p>玩家開局需輸入姓名並選擇性別。起始 HP、MP 均為 100 點。攻擊、防禦、速度、運氣為被動屬性，初始各 5 點。力量、敏捷、智力、體力、精神、魅力為主動屬性，初始各 1 點，透過升級獲得的「屬性可支配點數」自由分配，主動屬性會影響被動屬性與最大 HP/MP。</p>
    <p>加入宗門後才能使用採礦、砍柴、種田、狩獵、煉丹、煉器與畫符等技能；未加入宗門時只能修練。可透過「加入宗門」按鈕加入宗門。</p>
    <p>背包初始有 10 格，相同物品可以疊加，例如「入品丹藥(10)」。未來可以透過「儲物袋」來提升背包空間。</p>
    <p>商店功能與宗門系統將在後續版本開放，敬請期待。</p>
  </div>

<script>
const stageNames = [
  "凡人","練氣","築基","結丹","元嬰","化神","煉虛","合體","大乘","渡劫",
  "真仙","金仙","太乙金仙","大羅金仙","混元大羅","仙王","仙帝","天尊",
  "祖仙","無上","超脫輪迴","無上極境","混沌無極","大道歸一","宇宙之主",
  "創世真神","界外天尊","混元道尊","始源帝尊","太初聖皇","永恆之神",
  "混沌主宰","萬道歸宗","終極造物主","無限永存者","超維觀察者","超越者",
  "真理化身","虛無之神","界上界主","唯一真神","太上道祖","永劫不朽",
  "無極本源","多元彼岸主","原初意志","萬界始祖","終焉創世者",
  "絕對真理","超越唯一","永恆王座"
];
// 10層一境
const GRADE_NAMES = ["入品","下品","中品","上品","極品","超品","絕品","寶品","地品","天品"]; // 保留但不再顯示

let player = {
  name: "",        // 玩家姓名
  gender: "",      // 玩家性別
  level: 0,        // 小層序號
  exp: 0,
  attributePoints: 10, // 初始可支配點數
  joinedSect: false,   // 是否已加入宗門
  resources: { stone: 0, wood: 0, herb: 0, meat: 0, elixir: 0, gear: 0 },
  // 靈石與屬性
  spiritStones: 0,
  stats: {
    // 被動屬性
    hp: 100, maxHp: 100,
    mp: 100, maxMp: 100,
    attack: 5, defense: 5,
    speed: 5, luck: 5,
    // 主動屬性
    strength: 1, dexterity: 1,
    intelligence: 1, constitution: 1,
    spirit: 1, charisma: 1
  },
  cultivation: {
    root: 1,
    comprehension: 1,
    sectRank: "普通弟子"
  },
  equipment: {
    weapon: null,
    armor: null,
    accessory: null
  },
  bag: {
    capacity: 10,
    items: {}
  }
};
let currentAction = null;
let actionEndTime = 0;
const ACTION_TIME = {train:10, mine:15, farm:20, hunt:25, drawTalisman:20}; // 時間秒數，新增畫符
// 新增砍柴技能時間
ACTION_TIME.cutWood = 15;

function save() {
  localStorage.setItem("xiuxian_v1_save", JSON.stringify(player));
}

function load() {
  const data = localStorage.getItem("xiuxian_v1_save");
  if (data) {
    try {
      const obj = JSON.parse(data);
      // 混合保存資料與默認值，保證新欄位存在
      player = Object.assign({}, player, obj);
      // 如果缺少新屬性則補上
      // 確保新欄位存在
      player.name = player.name || "";
      player.gender = player.gender || "";
      player.attributePoints = (player.attributePoints !== undefined) ? player.attributePoints : 10;
      player.joinedSect = player.joinedSect || false;
      if (!player.resources) player.resources = {stone:0, wood:0, herb:0, meat:0, elixir:0, gear:0};
      // 確保資源欄位完整
      player.resources.stone = player.resources.stone || 0;
      player.resources.wood = player.resources.wood || 0;
      player.resources.herb = player.resources.herb || 0;
      player.resources.meat = player.resources.meat || 0;
      player.resources.elixir = player.resources.elixir || 0;
      player.resources.gear = player.resources.gear || 0;
      player.spiritStones = player.spiritStones || 0;
      if (!player.stats) {
        player.stats = {
          hp: 100, maxHp: 100,
          mp: 100, maxMp: 100,
          attack: 5, defense: 5,
          speed: 5, luck: 5,
          strength: 1, dexterity: 1,
          intelligence: 1, constitution: 1,
          spirit: 1, charisma: 1
        };
      } else {
        // 確保每個屬性都有值
        player.stats.hp = player.stats.hp || 100;
        player.stats.maxHp = player.stats.maxHp || player.stats.hp;
        player.stats.mp = player.stats.mp || 100;
        player.stats.maxMp = player.stats.maxMp || player.stats.mp;
        player.stats.attack = (player.stats.attack !== undefined) ? player.stats.attack : 5;
        player.stats.defense = (player.stats.defense !== undefined) ? player.stats.defense : 5;
        player.stats.speed = (player.stats.speed !== undefined) ? player.stats.speed : 5;
        player.stats.luck = (player.stats.luck !== undefined) ? player.stats.luck : 5;
        player.stats.strength = (player.stats.strength !== undefined) ? player.stats.strength : 1;
        player.stats.dexterity = (player.stats.dexterity !== undefined) ? player.stats.dexterity : 1;
        player.stats.intelligence = (player.stats.intelligence !== undefined) ? player.stats.intelligence : 1;
        player.stats.constitution = (player.stats.constitution !== undefined) ? player.stats.constitution : 1;
        player.stats.spirit = (player.stats.spirit !== undefined) ? player.stats.spirit : 1;
        player.stats.charisma = (player.stats.charisma !== undefined) ? player.stats.charisma : 1;
      }
      if (!player.cultivation) {
        player.cultivation = {root:1, comprehension:1, sectRank:"普通弟子"};
      } else {
        player.cultivation.root = player.cultivation.root || 1;
        player.cultivation.comprehension = player.cultivation.comprehension || 1;
        player.cultivation.sectRank = player.cultivation.sectRank || "普通弟子";
      }
      if (!player.equipment) {
        player.equipment = {weapon:null, armor:null, accessory:null};
      }
      if (!player.bag) {
        player.bag = {capacity:10, items:{}};
      } else {
        player.bag.capacity = player.bag.capacity || 10;
        player.bag.items = player.bag.items || {};
      }
    } catch(err) {
      console.error(err);
    }
  }
  // 若尚未設定姓名，顯示角色創建面板
  const startPanel = document.getElementById('startPanel');
  if (player.name === "" && startPanel) {
    startPanel.style.display = 'block';
  }
  updateUI();
}
function resetGame() {
  if (!confirm("確定要重置所有進度？")) return;
  player = {
    name: "",
    gender: "",
    level: 0,
    exp: 0,
    attributePoints: 10,
    joinedSect: false,
    resources: { stone: 0, wood: 0, herb: 0, meat: 0, elixir: 0, gear: 0 },
    spiritStones: 0,
    stats: {
      hp: 100, maxHp: 100,
      mp: 100, maxMp: 100,
      attack: 5, defense: 5,
      speed: 5, luck: 5,
      strength: 1, dexterity: 1,
      intelligence: 1, constitution: 1,
      spirit: 1, charisma: 1
    },
    cultivation: {
      root: 1,
      comprehension: 1,
      sectRank: "普通弟子"
    },
    equipment: {
      weapon: null,
      armor: null,
      accessory: null
    },
    bag: {
      capacity: 10,
      items: {}
    }
  };
  currentAction = null; actionEndTime=0;
  save(); updateUI();
  // 重置後重新顯示角色創建面板
  const sp = document.getElementById('startPanel');
  if (sp) sp.style.display = 'block';
}

function exportSave() {
  const data = JSON.stringify(player);
  const blob = new Blob([data], {type: "application/json"});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = "xiuxian_v1_save.json";
  a.click();
}

function importSave(e) {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = () => {
    try {
      const data = JSON.parse(reader.result);
      player = data; save(); updateUI();
    } catch(err) { alert("檔案格式錯誤"); }
  };
  reader.readAsText(file);
}

function getStage(level) {
  const stageIndex = Math.floor(level / 10);
  const layer = (level % 10) + 1;
  const stage = stageNames[Math.min(stageIndex, stageNames.length-1)];
  return stage + " " + layer + "層";
}
function getExpNeeded(level) {
  return 100 + level * 20;
}
function getGrade(level) {
  const index = Math.min(Math.floor(level/10), GRADE_NAMES.length-1);
  return GRADE_NAMES[index];
}

function updateUI() {
  // 每次更新前重新計算被動屬性
  recalcPassiveStats();
  // 更新基礎訊息
  document.getElementById('playerName').innerText = player.name || '-';
  document.getElementById('playerGender').innerText = player.gender || '-';
  document.getElementById('cultivationStage').innerText = getStage(player.level);
  document.getElementById('exp').innerText = player.exp;
  document.getElementById('expNeeded').innerText = getExpNeeded(player.level);
  document.getElementById('attributePoints').innerText = player.attributePoints;
  document.getElementById('spiritStones').innerText = player.spiritStones;
  // 被動屬性
  document.getElementById('attr_hp').innerText = player.stats.hp;
  document.getElementById('attr_mp').innerText = player.stats.mp;
  document.getElementById('attr_attack').innerText = player.stats.attack;
  document.getElementById('attr_defense').innerText = player.stats.defense;
  document.getElementById('attr_speed').innerText = player.stats.speed;
  document.getElementById('attr_luck').innerText = player.stats.luck;
  // 主動屬性
  document.getElementById('attr_strength').innerText = player.stats.strength;
  document.getElementById('attr_dexterity').innerText = player.stats.dexterity;
  document.getElementById('attr_intelligence').innerText = player.stats.intelligence;
  document.getElementById('attr_constitution').innerText = player.stats.constitution;
  document.getElementById('attr_spirit').innerText = player.stats.spirit;
  document.getElementById('attr_charisma').innerText = player.stats.charisma;
  // 修仙屬性與宗門
  document.getElementById('cult_root').innerText = player.cultivation.root;
  document.getElementById('cult_comprehension').innerText = player.cultivation.comprehension;
  document.getElementById('cult_sectRank').innerText = player.cultivation.sectRank;
  document.getElementById('sectStatus').innerText = player.joinedSect ? '已加入' : '未加入';
  // 裝備資訊顯示
  document.getElementById('equip_weapon').innerText = player.equipment.weapon || '無';
  document.getElementById('equip_armor').innerText = player.equipment.armor || '無';
  document.getElementById('equip_accessory').innerText = player.equipment.accessory || '無';
  // 資源顯示
  const resourceNames = { stone:'礦石', wood:'木材', herb:'靈草', meat:'獸肉', elixir:'丹藥', gear:'裝備' };
  for (const res in player.resources) {
    const span = document.getElementById('res_'+res);
    if (span) {
      span.innerText = resourceNames[res] + '：' + player.resources[res];
    }
  }
  // 當前行動顯示
  document.getElementById('currentAction').innerText = currentAction ? {
    train: '修練',
    mine: '採礦',
    cutWood: '砍柴',
    farm: '種田',
    hunt: '狩獵',
    drawTalisman: '畫符'
  }[currentAction] : '無';
  // 更新背包顯示
  updateBagUI();
}
function startAction(type) {
  // 如果已有行動，禁止新的行動
  if (currentAction) { alert("已有進行中的行動"); return; }
  // 檢查宗門條件，修練之外的技能需加入宗門
  if (type !== 'train' && !player.joinedSect) {
    alert('請先加入宗門才能使用此技能');
    return;
  }
  currentAction = type;
  actionEndTime = Date.now() + ACTION_TIME[type]*1000;
  updateUI();
}

function tick() {
  if (currentAction) {
    const remaining = Math.max(0, actionEndTime - Date.now());
    document.getElementById('actionTimer').innerText = "（剩餘 "+ Math.ceil(remaining/1000) + "秒）";
    if (remaining <= 0) finishAction();
  } else {
    document.getElementById('actionTimer').innerText = "";
  }
}
function finishAction() {
  let expGain = 0;
  switch (currentAction) {
    case "train": expGain = 10; break;
    case "mine": player.resources.stone++; expGain = 5; break;
    case "cutWood": player.resources.wood++; expGain = 6; break;
    case "farm": player.resources.herb++; expGain = 6; break;
    case "hunt": player.resources.meat++; expGain = 8; break;
    case "drawTalisman":
      // 畫符產生符咒物品
      if (addItemToBag('符咒', 1)) {
        expGain = 4;
      }
      break;
  }
  player.exp += expGain;
  // 升級判斷
  while (player.exp >= getExpNeeded(player.level)) {
    player.exp -= getExpNeeded(player.level);
    player.level++;
    // 每次升級給予可支配點數，例如5點
    player.attributePoints += 5;
  }
  currentAction = null;
  save(); updateUI();
}

// 新增函數：增加物品到背包，可堆疊相同物品
function addItemToBag(name, qty) {
  const bag = player.bag;
  if (bag.items[name]) {
    bag.items[name] += qty;
  } else {
    if (Object.keys(bag.items).length < bag.capacity) {
      bag.items[name] = qty;
    } else {
      alert('背包已滿，無法獲得 ' + name);
      return false;
    }
  }
  return true;
}

// 更新背包內容顯示
function updateBagUI() {
  const listEl = document.getElementById('bagList');
  if (!listEl) return;
  // 清空列表
  listEl.innerHTML = '';
  const items = player.bag.items;
  const keys = Object.keys(items);
  if (keys.length === 0) {
    const li = document.createElement('li');
    li.innerText = '（空）';
    listEl.appendChild(li);
  } else {
    keys.forEach(name => {
      const li = document.createElement('li');
      li.innerText = name + '(' + items[name] + ')';
      listEl.appendChild(li);
    });
  }
  const capEl = document.getElementById('bagCapacity');
  if (capEl) capEl.innerText = player.bag.capacity;
}

function showRefine(type) {
  // 未加入宗門不能煉丹/煉器
  if (!player.joinedSect) {
    alert('請先加入宗門才能使用煉丹或煉器');
    return;
  }
  if (type==="pill") {
    if (player.resources.herb>=3) {
      player.resources.herb-=3;
      // 煉丹生成入品丹藥並放入背包
      if (addItemToBag('入品丹藥', 1)) {
        alert("成功煉出丹藥！");
      }
    } else {
      alert("靈草不足，需要3個");
    }
  }
  if (type==="gear") {
    if (player.resources.stone>=2 && player.resources.meat>=2) {
      player.resources.stone-=2; player.resources.meat-=2;
      player.resources.gear++;
      alert("煉器成功！");
    } else alert("礦石與獸肉不足，需要2個礦石與2個獸肉");
  }
  save(); updateUI();
}

// 計算被動屬性：根據主動屬性更新攻擊、防禦、速度、運氣和最大HP/MP
function recalcPassiveStats() {
  // 基礎值
  const baseHp = 100;
  const baseMp = 100;
  const baseAttack = 5;
  const baseDefense = 5;
  const baseSpeed = 5;
  const baseLuck = 5;
  // 主動屬性加成
  player.stats.maxHp = baseHp + player.stats.constitution * 10;
  player.stats.hp = Math.min(player.stats.hp, player.stats.maxHp);
  player.stats.maxMp = baseMp + player.stats.intelligence * 10;
  player.stats.mp = Math.min(player.stats.mp, player.stats.maxMp);
  player.stats.attack = baseAttack + player.stats.strength * 2;
  player.stats.defense = baseDefense + player.stats.constitution * 2;
  player.stats.speed = baseSpeed + player.stats.dexterity;
  player.stats.luck = baseLuck + player.stats.charisma;
}

// 分配屬性點數到指定主動屬性
function allocateAttribute(attr) {
  if (player.attributePoints <= 0) {
    alert('可支配點數不足');
    return;
  }
  // 增加主動屬性
  player.stats[attr]++;
  player.attributePoints--;
  // 重新計算被動屬性
  recalcPassiveStats();
  save(); updateUI();
}

// 確認角色創建
function confirmStart() {
  const name = document.getElementById('inputName').value.trim();
  const genderEls = document.getElementsByName('gender');
  let gender = '';
  for (const el of genderEls) {
    if (el.checked) gender = el.value;
  }
  if (!name) { alert('請輸入姓名'); return; }
  player.name = name;
  player.gender = gender;
  document.getElementById('startPanel').style.display = 'none';
  save(); updateUI();
}

// 切換說明面板顯示/隱藏
function toggleRules() {
  const el = document.getElementById('gameRules');
  if (el.style.display === 'none' || el.style.display === '') {
    el.style.display = 'block';
  } else {
    el.style.display = 'none';
  }
}

// 加入宗門
function joinSect() {
  if (player.joinedSect) {
    alert('你已經加入宗門');
    return;
  }
  player.joinedSect = true;
  player.cultivation.sectRank = '外門弟子';
  alert('成功加入宗門！');
  save(); updateUI();
}

// 商店功能暫時為占位
function openShop() {
  alert('商店功能尚未開放');
}

load(); setInterval(tick, 1000);
</script>
</body>
</html>
