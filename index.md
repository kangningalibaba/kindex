<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>略和AI助手</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link
      rel="stylesheet"
      href="https://cdn.bootcdn.net/ajax/libs/font-awesome/6.4.0/css/all.min.css"
    />
    <style>
      .chat-container {
          height: calc(100vh - 3rem);
      }
      .sidebar {
          transition: all 0.3s ease;
          position: relative;
          min-width: 3rem;
      }
      .sidebar.collapsed {
          width: 3rem;
          overflow: hidden;
      }
      .sidebar.collapsed .sidebar-content {
          opacity: 0;
          pointer-events: none;
      }
      .sidebar.collapsed #toggleSidebar {
          position: absolute;
          right: 0.5rem;
          top: 0.5rem;
      }
      .progress-bar {
          height: 2px;
          background-color: #1677ff;
          width: 0%;
          transition: width 0.3s ease;
      }
      .favorite-btn, .delete-message-btn, .delete-history-btn {
          transition: all 0.2s ease;
      }
      .favorite-btn.active {
          color: #f59e0b !important;
      }
      .message-actions {
          opacity: 0;
          transition: opacity 0.2s ease;
      }
      .group:hover .message-actions {
          opacity: 1;
      }
    </style>
  </head>
  <body class="bg-gray-100">
    <div class="flex h-screen">
      <!-- 左侧功能区 -->
      <div id="sidebar" class="sidebar w-56 bg-white shadow-md flex flex-col">
        <!-- Logo区域 -->
        <div
          class="p-4 border-b border-gray-200 flex justify-between items-center"
        >
          <img src="AI与自动化实战/logo.png" alt="略和AI Logo" class="h-8 object-contain" />
          <button
            id="toggleSidebar"
            class="text-gray-500 hover:text-gray-700 ml-auto"
          >
            <i class="fas fa-chevron-left"> </i>
            <i class="fas fa-chevron-right hidden"> </i>
          </button>
        </div>
        <!-- 功能按钮区域 -->
        <div class="sidebar-content flex-1 overflow-y-auto p-4 space-y-2">
          <button
            id="newChatBtn"
            class="w-full flex items-center space-x-2 p-2 rounded hover:bg-gray-100 text-left"
          >
            <i class="fas fa-plus text-blue-500"> </i>
            <span> 新建会话 </span>
          </button>
          <div class="mt-4">
            <div class="text-gray-500 text-sm px-2 py-1">历史会话</div>
            <div id="historyList" class="space-y-1">
              <!-- 历史会话将通过JS动态生成 -->
            </div>
          </div>
        </div>
        <!-- 底部设置区域 -->
        <div class="sidebar-content p-4 border-t border-gray-200 space-y-2">
          <button
            id="settingsBtn"
            class="w-full flex items-center space-x-2 p-2 rounded hover:bg-gray-100 text-left"
          >
            <i class="fas fa-cog text-gray-500"> </i>
            <span> 设置 </span>
          </button>
          <button
            id="aboutBtn"
            class="w-full flex items-center space-x-2 p-2 rounded hover:bg-gray-100 text-left"
          >
            <i class="fas fa-info-circle text-gray-500"> </i>
            <span> 关于 </span>
          </button>
        </div>
      </div>
      <!-- 右侧聊天区域 -->
      <div class="flex-1 flex flex-col">
        <!-- 顶部标题栏 -->
        <div class="bg-white p-4 shadow-sm">
          <h1 class="text-lg font-medium">和略和AI助手的会话</h1>
        </div>
        <!-- 聊天内容区域 -->
        <div
          class="flex-1 overflow-y-auto p-4 space-y-4 bg-white px-[15%]"
          id="chatContentArea"
        >
          <div class="flex items-start space-x-3 group">
            <div
              class="w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center"
            >
              <i class="fas fa-robot text-blue-500"> </i>
            </div>
            <div class="bg-gray-100 p-3 rounded-lg max-w-3xl relative">
              <p>HI，我是略和AI助手，可以通过对话回答你的任何问题。</p>
              <div
                class="message-actions absolute right-2 top-2 flex space-x-1"
              >
                <button
                  class="favorite-btn p-1 text-gray-400 hover:text-yellow-500"
                  title="收藏"
                >
                  <i class="fas fa-star"> </i>
                </button>
                <button
                  class="delete-message-btn p-1 text-gray-400 hover:text-red-500"
                  title="删除"
                >
                  <i class="fas fa-trash"> </i>
                </button>
              </div>
            </div>
          </div>
        </div>
        <!-- 输入区域 -->
        <div class="bg-white p-4 border-t border-gray-200">
          <div class="flex items-center space-x-2 mb-2">
            <label class="inline-flex items-center">
              <input
                type="checkbox"
                class="form-checkbox text-blue-500"
                id="searchOnline"
              />
              <span class="ml-2"> 联网搜索 </span>
            </label>
            <button class="text-gray-500 hover:text-gray-700" id="uploadBtn">
              <i class="fas fa-paperclip"> </i>
            </button>
          </div>
          <div class="relative">
            <textarea
              id="messageInput"
              class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              rows="3"
              placeholder="输入你的问题..."
            >
            </textarea>
            <div class="progress-bar"></div>
          </div>
          <div class="flex justify-between items-center mt-3">
            <div class="flex space-x-2">
              <button
                class="px-3 py-1 text-sm bg-gray-100 rounded-full hover:bg-gray-200 common-question"
              >
                常用问题1
              </button>
              <button
                class="px-3 py-1 text-sm bg-gray-100 rounded-full hover:bg-gray-200 common-question"
              >
                常用问题2
              </button>
            </div>
            <div class="flex space-x-2">
              <button
                id="stopBtn"
                class="px-4 py-2 bg-gray-100 text-gray-700 rounded-lg hover:bg-gray-200 hidden"
              >
                停止
              </button>
              <button
                id="sendBtn"
                class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
              >
                发送
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- 设置模态框 -->
    <div
      id="settingsModal"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 hidden"
    >
      <div class="bg-white rounded-lg p-6 w-full max-w-md">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-medium">设置LLM连接</h3>
          <button id="closeSettings" class="text-gray-500 hover:text-gray-700">
            <i class="fas fa-times"> </i>
          </button>
        </div>
        <div class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">
              模型类型
            </label>
            <select class="w-full p-2 border border-gray-300 rounded">
              <option>OpenAI</option>
              <option>Anthropic</option>
              <option>Local</option>
            </select>
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">
              模型名称
            </label>
            <input
              type="text"
              class="w-full p-2 border border-gray-300 rounded"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">
              基础URL
            </label>
            <input
              type="text"
              class="w-full p-2 border border-gray-300 rounded"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">
              API Key
            </label>
            <input
              type="password"
              class="w-full p-2 border border-gray-300 rounded"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">
              最大Token数
            </label>
            <input
              type="number"
              class="w-full p-2 border border-gray-300 rounded"
            />
          </div>
        </div>
        <div class="mt-6 flex justify-end space-x-3">
          <button
            id="saveSettings"
            class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
          >
            保存
          </button>
          <button
            id="cancelSettings"
            class="px-4 py-2 bg-gray-200 text-gray-700 rounded hover:bg-gray-300"
          >
            取消
          </button>
        </div>
      </div>
    </div>
    <!-- 关于模态框 -->
    <div
      id="aboutModal"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 hidden"
    >
      <div class="bg-white rounded-lg p-6 w-full max-w-md">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-medium">关于略和AI</h3>
          <button id="closeAbout" class="text-gray-500 hover:text-gray-700">
            <i class="fas fa-times"> </i>
          </button>
        </div>
        <div class="space-y-3">
          <p class="text-sm">版本: v1.0.0</p>
          <p class="text-sm">开发者: 略和AI团队</p>
          <p class="text-sm">版权所有 © 2023 略和AI</p>
          <p class="text-sm text-gray-500">本产品仅供学习交流使用</p>
        </div>
        <div class="mt-6 flex justify-end">
          <button
            id="closeAboutBtn"
            class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
          >
            确定
          </button>
        </div>
      </div>
    </div>
    <script>
      // 存储历史对话数据
      var chatHistory = {
        "favorites": [],
        "1": {
          title: "Ollama加载Qwen3模型失败",
          messages: [{
            sender: "user",
            content: "如何解决Ollama加载Qwen3模型失败的问题？"
          }, {
            sender: "ai",
            content: "请检查您的Ollama版本是否支持Qwen3模型..."
          }]
        },
        "2": {
          title: "Xinference服务器目录挂载",
          messages: [{
            sender: "user",
            content: "如何在Xinference服务器上挂载目录？"
          }, {
            sender: "ai",
            content: "您可以使用docker的-v参数来挂载目录..."
          }]
        },
        "3": {
          title: "Kuli云订阅转V2Ray配置指南",
          messages: [{
            sender: "user",
            content: "如何将Kuli云订阅转换为V2Ray配置？"
          }, {
            sender: "ai",
            content: "您可以使用第三方工具如v2rayN来转换..."
          }]
        }
      };
      document.addEventListener('DOMContentLoaded', function () {
        // 初始化历史会话列表
        renderHistoryList();

        // 设置和关于模态框功能
        var settingsModal = document.getElementById('settingsModal');
        var aboutModal = document.getElementById('aboutModal');
        document.getElementById('settingsBtn').addEventListener('click', function () {
          settingsModal.classList.remove('hidden');
        });
        document.getElementById('aboutBtn').addEventListener('click', function () {
          aboutModal.classList.remove('hidden');
        });
        document.getElementById('closeSettings').addEventListener('click', function () {
          settingsModal.classList.add('hidden');
        });
        document.getElementById('closeAbout').addEventListener('click', function () {
          aboutModal.classList.add('hidden');
        });
        document.getElementById('cancelSettings').addEventListener('click', function () {
          settingsModal.classList.add('hidden');
        });
        document.getElementById('closeAboutBtn').addEventListener('click', function () {
          aboutModal.classList.add('hidden');
        });
        document.getElementById('saveSettings').addEventListener('click', function () {
          settingsModal.classList.add('hidden');
          alert('设置已保存');
        });

        // 侧边栏折叠功能
        var toggleSidebar = document.getElementById('toggleSidebar');
        var sidebar = document.getElementById('sidebar');
        toggleSidebar.addEventListener('click', function () {
          sidebar.classList.toggle('collapsed');
          var chevronLeft = toggleSidebar.querySelector('.fa-chevron-left');
          var chevronRight = toggleSidebar.querySelector('.fa-chevron-right');
          chevronLeft.classList.toggle('hidden');
          chevronRight.classList.toggle('hidden');
        });

        // 发送消息功能
        var messageInput = document.getElementById('messageInput');
        var sendBtn = document.getElementById('sendBtn');
        var stopBtn = document.getElementById('stopBtn');
        var chatContent = document.getElementById('chatContentArea');
        var progressBar = document.querySelector('.progress-bar');
        sendBtn.addEventListener('click', sendMessage);
        messageInput.addEventListener('keydown', function (e) {
          if (e.key === 'Enter' && !e.shiftKey) {
            e.preventDefault();
            sendMessage();
          }
        });
        function sendMessage() {
          var message = messageInput.value.trim();
          if (!message) return;

          // 检查是否是第一条用户消息
          var isFirstUserMessage = document.querySelectorAll('#chatContentArea .flex').length === 1;

          // 添加用户消息
          addMessage(message, 'user');
          messageInput.value = '';

          // 如果是第一条用户消息，添加到历史记录
          if (isFirstUserMessage) {
            addToHistory(message);
          }

          // 显示加载状态
          sendBtn.classList.add('hidden');
          stopBtn.classList.remove('hidden');
          progressBar.style.width = '100%';

          // 模拟AI回复
          setTimeout(function () {
            progressBar.style.width = '0%';
            stopBtn.classList.add('hidden');
            sendBtn.classList.remove('hidden');
            var aiResponse = getAIResponse(message);
            addMessage(aiResponse, 'ai');
          }, 1500);
        }
        function addMessage(content, sender) {
          var messageDiv = document.createElement('div');
          messageDiv.className = "flex items-start space-x-3 ".concat(sender === 'user' ? 'justify-end' : '');
          if (sender === 'ai') {
            messageDiv.innerHTML = "\n                        <div class=\"w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center\">\n                            <i class=\"fas fa-robot text-blue-500\"></i>\n                        </div>\n                        <div class=\"bg-gray-100 p-3 rounded-lg max-w-3xl relative\">\n                            <p>".concat(content, "</p>\n                            <div class=\"message-actions absolute right-2 top-2 flex space-x-1\">\n                                <button class=\"favorite-btn p-1 text-gray-400 hover:text-yellow-500\" title=\"\u6536\u85CF\">\n                                    <i class=\"fas fa-star\"></i>\n                                </button>\n                                <button class=\"delete-message-btn p-1 text-gray-400 hover:text-red-500\" title=\"\u5220\u9664\">\n                                    <i class=\"fas fa-trash\"></i>\n                                </button>\n                            </div>\n                        </div>\n                    ");
          } else {
            messageDiv.innerHTML = "\n                        <div class=\"bg-blue-500 text-white p-3 rounded-lg max-w-3xl relative\">\n                            <p>".concat(content, "</p>\n                            <div class=\"message-actions absolute right-2 top-2 flex space-x-1\">\n                                <button class=\"favorite-btn p-1 text-white hover:text-yellow-300\" title=\"\u6536\u85CF\">\n                                    <i class=\"fas fa-star\"></i>\n                                </button>\n                                <button class=\"delete-message-btn p-1 text-white hover:text-red-300\" title=\"\u5220\u9664\">\n                                    <i class=\"fas fa-trash\"></i>\n                                </button>\n                            </div>\n                        </div>\n                        <div class=\"w-8 h-8 rounded-full bg-gray-200 flex items-center justify-center\">\n                            <i class=\"fas fa-user text-gray-600\"></i>\n                        </div>\n                    ");
          }
          chatContent.appendChild(messageDiv);
          chatContent.scrollTop = chatContent.scrollHeight;

          // 存储当前对话消息
          var currentTitle = document.querySelector('.bg-white.p-4.shadow-sm h1').textContent;
          if (!chatHistory[currentTitle]) {
            chatHistory[currentTitle] = {
              title: currentTitle,
              messages: []
            };
          }
          chatHistory[currentTitle].messages.push({
            sender: sender,
            content: content
          });
        }
        function getAIResponse(message) {
          var responses = ["我已经理解了你的问题：" + message + "。这是一个很好的问题，让我为你详细解答...", "关于" + message + "，我可以提供以下信息...", "这个问题涉及到多个方面，让我为你逐步分析..."];
          return responses[Math.floor(Math.random() * responses.length)];
        }

        // 停止按钮功能
        stopBtn.addEventListener('click', function () {
          progressBar.style.width = '0%';
          stopBtn.classList.add('hidden');
          sendBtn.classList.remove('hidden');
          addMessage("对话已停止", 'ai');
        });

        // 渲染历史会话列表
        function renderHistoryList() {
          var historyList = document.getElementById('historyList');
          historyList.innerHTML = '';
          var _loop = function _loop(id) {
            if (id !== 'favorites') {
              var history = chatHistory[id];
              var historyItem = document.createElement('div');
              historyItem.className = 'flex items-center justify-between w-full group';
              historyItem.innerHTML = "\n                            <button class=\"history-btn flex-1 flex items-center space-x-2 p-2 rounded hover:bg-gray-100 text-left\" data-id=\"".concat(id, "\">\n                                <i class=\"fas fa-comment text-gray-400\"></i>\n                                <span>").concat(history.title, "</span>\n                            </button>\n                            <button class=\"delete-history-btn p-2 text-gray-400 hover:text-red-500 opacity-0 group-hover:opacity-100\" data-id=\"").concat(id, "\">\n                                <i class=\"fas fa-trash\"></i>\n                            </button>\n                        ");
              historyList.appendChild(historyItem);

              // 添加点击事件
              historyItem.querySelector('.history-btn').addEventListener('click', function () {
                loadChatHistory(id);
              });
              historyItem.querySelector('.delete-history-btn').addEventListener('click', function (e) {
                e.stopPropagation();
                deleteHistory(id);
              });
            }
          };
          for (var id in chatHistory) {
            _loop(id);
          }
        }

        // 加载历史对话
        function loadChatHistory(chatId) {
          var history = chatHistory[chatId];
          if (!history) return;

          // 更新标题
          document.querySelector('.bg-white.p-4.shadow-sm h1').textContent = history.title;

          // 清空当前聊天内容
          var chatContent = document.getElementById('chatContentArea');
          chatContent.innerHTML = '';

          // 加载历史消息
          history.messages.forEach(function (msg) {
            addMessage(msg.content, msg.sender);
          });
        }

        // 删除历史记录
        function deleteHistory(historyId) {
          if (confirm('确定要删除这条历史记录吗？')) {
            delete chatHistory[historyId];
            renderHistoryList();

            // 如果删除的是当前显示的对话，重置为默认界面
            var currentTitle = document.querySelector('.bg-white.p-4.shadow-sm h1').textContent;
            if (chatHistory[historyId] && chatHistory[historyId].title === currentTitle) {
              resetChatInterface();
            }
          }
        }

        // 添加到历史记录
        function addToHistory(message) {
          var chatId = Date.now().toString();
          var title = message.substring(0, 20) + (message.length > 20 ? '...' : '');
          chatHistory[chatId] = {
            title: title,
            messages: []
          };
          renderHistoryList();
        }

        // 重置聊天界面
        function resetChatInterface() {
          document.querySelector('.bg-white.p-4.shadow-sm h1').textContent = '和略和AI助手的会话';
          document.getElementById('chatContentArea').innerHTML = "\n                    <div class=\"flex items-start space-x-3 group\">\n                        <div class=\"w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center\">\n                            <i class=\"fas fa-robot text-blue-500\"></i>\n                        </div>\n                        <div class=\"bg-gray-100 p-3 rounded-lg max-w-3xl relative\">\n                            <p>HI\uFF0C\u6211\u662F\u7565\u548CAI\u52A9\u624B\uFF0C\u53EF\u4EE5\u901A\u8FC7\u5BF9\u8BDD\u56DE\u7B54\u4F60\u7684\u4EFB\u4F55\u95EE\u9898\u3002</p>\n                            <div class=\"message-actions absolute right-2 top-2 flex space-x-1\">\n                                <button class=\"favorite-btn p-1 text-gray-400 hover:text-yellow-500\" title=\"\u6536\u85CF\">\n                                    <i class=\"fas fa-star\"></i>\n                                </button>\n                                <button class=\"delete-message-btn p-1 text-gray-400 hover:text-red-500\" title=\"\u5220\u9664\">\n                                    <i class=\"fas fa-trash\"></i>\n                                </button>\n                            </div>\n                        </div>\n                    </div>\n                ";
        }

        // 收藏消息
        function favoriteMessage(message, sender) {
          chatHistory.favorites.push({
            sender: sender,
            content: message,
            time: new Date().toISOString()
          });
          alert('已收藏消息');
        }

        // 删除消息
        function deleteMessage(messageElement) {
          if (confirm('确定要删除这条消息吗？')) {
            messageElement.remove();
          }
        }

        // 新建会话功能
        document.getElementById('newChatBtn').addEventListener('click', function () {
          var messages = document.querySelectorAll('#chatContentArea .flex');

          // 如果有对话内容，保存第一条用户消息到历史记录
          if (messages.length > 1) {
            var firstUserMessage = '';
            for (var i = 1; i < messages.length; i++) {
              if (messages[i].querySelector('.fa-user')) {
                firstUserMessage = messages[i].querySelector('p').textContent;
                break;
              }
            }
            if (firstUserMessage) {
              var chatId = Date.now().toString();
              var title = firstUserMessage.substring(0, 20) + (firstUserMessage.length > 20 ? '...' : '');
              chatHistory[chatId] = {
                title: title,
                messages: []
              };
              messages.forEach(function (msg) {
                var sender = msg.querySelector('.fa-robot') ? 'ai' : 'user';
                var content = msg.querySelector('p').textContent;
                chatHistory[chatId].messages.push({
                  sender: sender,
                  content: content
                });
              });
              renderHistoryList();
            }
          }

          // 重置聊天界面
          resetChatInterface();
        });

        // 常用问题点击功能
        document.querySelectorAll('.common-question').forEach(function (btn) {
          btn.addEventListener('click', function () {
            messageInput.value = this.textContent.trim();
            messageInput.focus();
          });
        });
      });
    </script>
  </body>
</html>
