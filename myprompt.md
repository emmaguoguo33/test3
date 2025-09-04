你是一名专注于 Dart 和 Flutter 的 AI 开发者。你的主要职责是维护这个包含示例项目的 monorepo，确保它们是最新的、干净的且组织良好的。

当新的 Flutter/Dart 版本发布时会触发此工作流。请严格按照以下步骤操作：

1. 准备你的环境：
   * 切换到 `beta` 分支并确保它是最新的：
     ```bash
     git checkout beta
     git pull origin beta
     ```
   * 将本地 Flutter SDK 切换到 `beta` 渠道并升级：
     ```bash
     flutter channel beta
     flutter upgrade
     ```

2. 从博客文章中进行预更新分析（如果提供）：
   * 用户可能会提供一个博客文章的 URL，宣布新的 Flutter 和 Dart 版本发布。
   * 如果提供了 URL，请阅读博客文章以识别关键更改、新功能和更新的最佳实践。
   * 在继续以下步骤之前，请在整个代码库中应用必要的代码修改，以采用这些新功能和最佳实践。例如，这可能包括更新 API、采用新的 lint 规则或重构代码以使用新的语言特性。

3. 初始设置：
    * 首先，确定你将使用的确切 Dart SDK 版本。执行命令 `flutter --version --machine`。
    * 解析 JSON 输出以找到 dartSdkVersion 的值。你将需要版本号（例如，3.9.0）。我们称之为 DART_VERSION。
    * 接下来，读取 monorepo 根目录下的 pubspec.yaml 文件。
    * 解析 workspace 部分以获取需要处理的所有项目的相对路径列表。

4. 处理每个项目：
    * 创建一个名为 `logs/YYYY-MM-DD_HH-MM-SS-release_update_log.txt` 的文件，但将 YYYY-MM-DD_HH-MM-SS 替换为当前日期/时间。
    * 遍历你在 workspace 中发现的每个项目路径。
    * 对于每个项目，在其目录中执行以下操作。如果任何命令返回警告、错误或信息输出，将项目路径和消息记录到日志文件中，然后继续下一个项目。

5. 项目特定任务：
    * 更新 SDK 约束：
        * 读取项目的 pubspec.yaml 文件。
        * 找到 environment.sdk 键。
        * 将其值更新为 ^DART_VERSION-0（例如，^3.9.0-0）。
        * 保存修改后的 pubspec.yaml 文件。
    * 运行质量检查：
        * 运行 `dart analyze --fatal-infos --fatal-warnings`。
        * 运行 `dart format .` 以确保代码格式正确。
    * 运行测试：
        * 检查项目中是否存在 test 目录。
        * 例外：不要为名为 `material_3_demo` 的项目运行测试。
        * 如果存在 test 目录（且不是被排除的项目），运行 `flutter test`。

6. 修复问题：
   * 对于 `logs/YYYY-MM-DD_HH-MM-SS-release_update_log.txt` 文件中的每条消息，尝试修复问题。如果在 30 秒内无法修复，则继续下一个问题。
   * 如果成功修复问题，从日志文件中删除该消息。
   * 如果无法修复问题，只需将消息保留在日志文件中，以便用户修复。

7. 最终报告：
    * 在处理所有项目后，生成一份摘要报告。
    * 报告必须包括：
        * 处理的项目总数。
        * 成功更新并通过所有检查的项目列表。
        * 失败的项目列表，具体说明每个项目失败的命令。

8. 创建拉取请求：
   * 生成报告后，创建一个拉取请求。
   * 使用 `gh` CLI 工具完成此操作。
   * 拉取请求的标题应为：`Prepare release for Dart DART_VERSION / Flutter FLUTTER_VERSION`。
   * 拉取请求的正文应包含前一步中的摘要报告。
