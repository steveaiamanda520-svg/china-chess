# 弈境 · 中国象棋

完整手机象棋游戏源码、文生图美术原图与网页资源。

## 本地启动

```sh
python3 -m http.server 8080 --directory dist
```

浏览器打开 http://localhost:8080 。请通过 HTTP 服务访问，以支持人机对弈的 Web Worker。

## 运行测试

```sh
node tests/engine.test.cjs
node tests/tactics.test.cjs
node tests/clock.test.cjs
node tests/stress.cjs
```

为手机触屏打造的中国象棋网页。无第三方运行时依赖，静态部署。

## 运行

用静态服务器托管 `dist/`。`worker.js` 需要 HTTP(S) 同源访问。

## 美术

使用文生图生成四张独立棋盘材质和白玉/墨玉棋子图集。完整源图在 `design/`，WebP 游戏素材在 `dist/assets/`。棋线、字形、落点、将军提示由代码精确绘制，不依赖生成图中的文字或棋格。

设计方向：正交俯视，宋式雅致，白玉与墨玉棋子、双圈金嵌线；黄花梨、深绿墨玉、汝窑青瓷、深朱漆四款棋盘；克制山水纹样留在边缘，中央区域低对比以保障辨识。

文生图提示词共同约束：production xiangqi game material texture; perfect orthographic top down; edge-to-edge rectangle; refined Chinese luxury craftsmanship; even soft light; low-contrast unobstructed playing area; no pieces, grid, writing, text, perspective or UI. 材质提示分别为 aged huanghuali hardwood / dark imperial green nephrite / pale celadon Ru-ware fine crackle / deep oxblood cinnabar lacquer。棋子提示为 two isolated blank circular top-down jade tokens, ivory and emerald black, shallow recessed faces, fine champagne gold concentric inlays, genuinely transparent background。

## 功能

- 三档本地人机、双人同屏、悔棋、提示、棋盘翻转。
- 点击与拖动落子，合法落点具有整格触摸范围。
- 将军、将死和困毙检查；三次重复局面简化判和。
- 棋谱逐步复盘、自动续局、音效与支持设备的振动反馈。

## 性能

- AI 在可取消的 Web Worker 内执行；不阻塞界面。
- 将军检测从将帅向外搜索，避免反复枚举所有敌方棋子的着法。
- 迭代加深、Alpha-Beta、置换表、吃子静态搜索、杀手与历史启发排序、增量估值。
- 棋子用合成位移进行移动；拖动中只更新单颗棋子的位移。
- 保存延迟到操作之后，并在页面离开/进入后台时同步落盘。
- 后台思考可随悔棋、重开与切后台中断，旧任务结果按请求编号过滤。

## 验证

运行：

```sh
node tests/engine.test.cjs
node tests/stress.cjs
```

已验证：16 组规则/引擎测试、初始深度 3 的 79,666 个叶节点、1,200 个可达局面与旧引擎逐着对比、3,000 个生成局面与独立慢速将军检测对比、120 步自动合法对弈。

通过浏览器检查 320 / 360 / 393 / 430 / 768 像素布局。检查了实际落子、人机应手、思考期间悔棋、拖动落子、皮肤切换、翻转、刷新续局、棋谱前后翻页以及复盘返回实战。布局检查使用 iframe 尺寸，不代表 iOS/Android 真机性能测试。

性能数字仅代表当前开发环境，不承诺用户设备帧率或耗时。当前没有联网匹配、排名、账号棋谱云同步，未进行 Elo 棋力标定；重复局面使用休闲规则，不包含完整竞赛长将长捉裁定。

## 本轮更新：大厅、计时与青花瓷

首页先选择人机/联机/排位/同屏入口，开局前必须明确选择红黑、每方局时和每步时限。联机及排位仅有设置入口，界面明确标注服务待接入，不模拟匹配。人机支持执黑，电脑执红先行。对局独立页面不显示大厅或棋具展示区。

新增实际局时与步时倒计时、超时判负、返回大厅暂停与续局。计时按墙钟计算，切后台仍计时，悔棋不返还已耗局时。

青花瓷棋子使用独立文生图素材 `design/porcelain-tokens-source.png`，游戏中使用 `dist/assets/porcelain-tokens.webp`。提示词：two isolated top-down circular Qinghua porcelain tokens, ivory with cobalt floral rim and cobalt with white floral rim; recessed blank faces for programmatic lettering; equal size at x25%/75%; true transparent background; glazed highlights; no text, board or perspective。

红点加 ! 表示己方棋子可被对手合法直接吃掉，绿点加 + 表示己方有合法吃法的敌子；选子后红色落点代表走到该处可被对手直接吃掉。按当前局面计算并排除导致攻击方将帅被将军的非法吃法；不是对后续安全或得子的保证。风险提示可关闭，棋子材质可独立换回双玉。

新增验证：`node tests/tactics.test.cjs` 与 `node tests/clock.test.cjs`。浏览器实际检查了三项必选校验、执黑电脑先行、纯对局布局、15 秒步时超时、红绿风险点和危险吃子落点。

## 水墨首页与危险红圈

首页和开局设置改为宣纸、墨绿、细线边框风格。新增文生图水墨山水横幅 `design/lobby-landscape-source.png`，发布使用 `dist/assets/lobby-landscape.webp`；画面包含松枝、白鹤、远山、河流、亭台，左侧留白叠加原生文字。模式入口使用线描图标和水纹装饰。生成提示词：Song dynasty ink wash landscape on ivory xuan paper; sage mountains, river, pavilion, pine branch and two cranes; empty low-contrast left 40%; details on right; no text, UI, seals or game grid。

己方受威胁棋子保留 ! 红点，并增加 3px 鲜红外圈、浅色细描边和静态柔光；不使用闪烁或循环动画。首页外观调整不影响纯棋盘对局页面。

## 文生图界面图标

界面图标全部改用同一张透明文生图图集：`design/ui-icon-atlas-source.png` 保存生成原图，`dist/assets/ui-icon-atlas.webp` 为 800 × 1000 的游戏资源。20 枚图标按照四列五行排布：人机、联机、排位、同屏；设置、悔棋、提示、翻转；重开、返回、前进、关闭；棋具、棋谱、危险、可吃；品牌、音效、触感、计时。材质为青绿玉石、象牙白与古金，含克制云纹。

提示词摘要：one transparent 4-column 5-row production sprite atlas; isolated consistently sized icons centered in square cells; Song dynasty ink-and-jade styling, pine green, ivory jade, antique gold, legible silhouettes, subtle carved cloud details; exact 20 icon functions as listed; no labels, words, backgrounds or grid. 使用内置文生图生成，随后进行网页资源尺寸和格式优化。

原 SVG 导航图标与 Unicode 操作符已替换。棋盘的精确棋线与风险外圈属于功能性图形，继续由程序绘制；棋字继续使用文字，以保持规则与可读性。图标使用透明图集共享下载，所有可操作按钮仍保留文字或 aria-label，图片不接管点击事件。
