# CollisionMAPactor

UE 5.5 关卡 Actor 凸包替换工具 —— 处理简模（Convex Hull Replacement Tool）

## 功能简介

将选中的 StaticMesh 替换为其凸包简模版本，并批量更新关卡中所有使用该模型的 Actor。

- 自动检测/创建凸包碰撞体（凸包简模 `_Convex`）
- 扫描当前关卡，找到所有引用该模型的 Actor
- 保留原始 Transform 和材质覆盖
- 支持 `Ctrl+Z` 撤销全部替换

## 使用方法

1. 在 Unreal Engine 5.5 内容浏览器中选中一个或多个 **StaticMesh** 资产
2. 打开 **工具 > Python 脚本控制台**（或使用 Editor Utility Widget）
3. 运行以下命令：

```python
import importlib, convex_hull_replacement
importlib.reload(convex_hull_replacement)
```

或直接在 UE Python 控制台中粘贴并运行 `convex_hull_replacement.py` 文件的全部内容。

## 配置参数

在 `convex_hull_replacement.py` 文件顶部可调整以下参数：

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `SUFFIX` | `"_Convex"` | 凸包简模资产名后缀 |
| `AUTO_HULL_COUNT` | `4` | 自动生成凸包时的凸包数量 |
| `AUTO_MAX_HULL_VERTS` | `32` | 每个凸包的最大顶点数 |
| `AUTO_HULL_PRECISION` | `100000` | 凸包分解精度 |

## 流程说明

```
选中模型 → 检测/创建凸包简模 → 扫描关卡 → 替换 Actor
```

1. **检测简模**：检查同目录下是否已有 `<名称>_Convex` 资产，有则直接使用
2. **创建简模**：若无简模，自动通过凸包分解生成，经 FBX 导出 → 处理 → 导入流程获得纯凸包网格
3. **扫描关卡**：遍历当前关卡所有 Actor，找到使用原始模型的组件
4. **替换 Actor**：在可撤销事务中批量替换组件的 StaticMesh

## 环境要求

- Unreal Engine 5.5
- 已启用 Python 插件（`Python Editor Script Plugin`）