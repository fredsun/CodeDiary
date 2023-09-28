1. PyQt5创建GUI用户模型时，生成.ui, 需要转换为.py，需在pycharm另行配置
2. PyUIC, 必须配置Arguments, $FileName$ -o $FileNameWithoutExtension$.py ,否则报错 Error: one input ui-file must be specified
3. PyUIC, 使用的不是python.exe，而是pyuic5.exe
4. 虚拟环境中千万别用'pip install --upgrade pip'更新pip，这会破坏pip。