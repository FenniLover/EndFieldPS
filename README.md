# EndField PS
[EN](README.md) | [IT](docs/README_it-IT.md) | [RU](docs/README_ru-RU.md)

![Logo](https://socialify.git.ci/SuikoAkari/EndFieldPS/image?custom_description=Private+server+for+EndField&amp;description=1&amp;font=Jost&amp;forks=1&amp;issues=1&amp;language=1&amp;logo=https%3A%2F%2Farknights.wiki.gg%2Fimages%2F3%2F31%2FArknights_Endfield_logo.png&amp;name=1&amp;pattern=Circuit+Board&amp;pulls=1&amp;stargazers=1&amp;theme=Dark)

EndFieldPS是服务于EndField CBT2 的私人服务器。

## 已实现功能

* 登录
* 角色切换
* 队伍切换
* 场景切换 (暂时适用于某些场景)
* 使用 MongoDB 保存数据

## 安装步骤

1. 安装 [.NET SDK](https://dotnet.microsoft.com/en-us/download) (建议使用 8.0.12版本), [MongoDB](https://www.mongodb.com/try/download/community) 以及 [Fiddler Classic](https://www.telerik.com/fiddler/fiddler-classic) 或者使用 [mitmproxy](https://mitmproxy.org/) 如果您还没有
   * 在安装 *Fiddler Classic*时, 确保 **enable（启用）** "Decrypt HTTPS traffic（解密 HTTPS 流量）" 并 **install（安装）** 证书！
   * 你必须通过“工具”（即菜单栏左上角） -> Options -> HTTPS -> 勾选 "Capture HTTPS CONNECTs" 以及 "Decrypt HTTPS traffic". 当然你也可以选择操作 (在"Capture HTTPS CONNECTs"旁边的) -> Trust Root Certificate 然后按下 "Yes"
2. 下载 [预编译构建版本](https://github.com/SuikoAkari/EndFieldPS/releases/latest) 或自行构建。
3.  将 Json 和 TableCfg 文件夹放入与 EndFieldPS.exe 同一目录的文件夹（可在 [此处](https://github.com/PotRooms/EndFieldData/tree/main) 下载需要的副本）。
4. 运行服务器 (`EndFieldPS.exe`)
5. 用以下代理脚本进行覆盖`C:\Users\<YourUserName>\Documents\Fiddler2\Scripts\CustomRules.js`路径中的脚本（或备份默认脚本并创建一个同名的新文件）：
    * 您也可以选择运行 *Fiddler Classic*, 进入 `Rules -> Customize Rules` （CTRL + R）并保存，或选择 “*FiddlerScript*”选项卡。

    ```javascript
    import System;
    import System.Windows.Forms;
    import Fiddler;
    import System.Text.RegularExpressions;

    class Handlers
    {
        static function OnBeforeRequest(oS: Session) {
            if(oS.host.Contains("gryphline.com") || oS.host.Contains("hg-cdn.com")) {
                if(oS.HTTPMethodIs("CONNECT")){
                    return;
                }
                FiddlerObject.log(">>>>>>>>>>>> URL:" + oS.fullUrl);
                oS.oRequest.headers.UriScheme = "http";
                oS.oRequest["Cookie"] = (oS.oRequest["Cookie"] + ";OriginalHost=" + oS.host + ";OriginalUrl=" + oS.fullUrl);
                oS.host = "localhost:5000";
            }
        }
    };
    ```

    或者，您也可以使用 mitmproxy 命令：

    ```shell
    mitmproxy -s ak.py
    ```

    ak.py:

    ```py
    import mitmproxy
    from mitmproxy import ctx, http
    class EndFieldModifier:
        def requestheaders(self,flow: mitmproxy.http.HTTPFlow):
            if "gryphline.com" in flow.request.host or "hg-cdn.com" in flow.request.host:
                if flow.request.method=="CONNECT":
                    return
                
                flow.request.scheme="http"
                flow.request.cookies.update({
                    "OriginalHost":flow.request.host,
                    "OriginalUrl":flow.request.url
                })
                flow.request.host="localhost"
                flow.request.port=5000
                ctx.log.info("URL:"+flow.request.url)
                
                
                
    addons=[
        EndFieldModifier()
    ]
    ```

6. 运行 *Fiddler Classic* - 它应该以新的 *Custom Rules script*启动（您可以在 *FiddlerScript* 选项卡中查看）。
7. 运行游戏客户端并开始游戏！(注：目前仅支持OS（即国际服）客户端）
8. 您必须在服务器控制台中使用 “account create （你的用户名）”创建一个账户，然后使用类似“（用户名）@随意的邮箱地址.你想要的地址后缀 ”的电子邮件登录游戏：如（123@1.com）。由于没有密码机制，您可以在输入一个随机密码。

## Discord

如果您想讨论或帮助这个项目，请加入我们的 [Discord 服务器](https://discord.gg/gPvqhfdMU6)！

## 备注

创建此项目的目的并非取代官方服务器。如需移除请求或说明，请通过 Telegram 联系我： @SuikoAkari.
