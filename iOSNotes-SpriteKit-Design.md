## SpriteKit

1. node 是通过名字查找, 可以存储 名字 字段.
2. node 显示有 zposition 属性, 考虑图层关系
3. 常量数据怎么保存？直接使用 或 新建plist
4. Layer : 每层都添加一个rootNode, 方便管理nodes
5. Folder Helpers : SoundManager : Singleton Manager : all sounds
6. GameplayKit : Entity 指对象类型, Component 指功能和动作
7. GameplayKit : State Machine 状态机
8. SKScene : the root node. The SKView class’s textureFromNode: method can render a node tree’s content into a texture. The texture is sized so that it holds the contents of the node and all of its visible descendants.
9. an archive : node with a name property