檔案內容 AI 分析與處理自動化 Workflow
這個 n8n workflow 是一個自動化專案，用於監控特定本地資料夾中的新檔案，自動讀取檔案內容，根據檔案類型進行處理，並利用 AI 模型對內容進行分析或轉換，最後將 AI 的結果儲存到另一個檔案。

解決的問題
在日常工作或個人專案中，我們經常需要處理不同格式的檔案（如文字檔、試算表、PDF 等），並可能需要從中提取資訊、進行總結、翻譯或執行其他基於內容的 AI 任務。手動處理這些檔案既耗時又重複。

這個 workflow 旨在自動化這個過程，一旦有新檔案出現在監控資料夾中，它就能自動完成後續的內容提取、AI 分析和結果儲存，大大提高效率。

功能特色
自動監控資料夾： 監控本地指定資料夾，當有新檔案加入時自動觸發。

多格式檔案處理： 支援處理 .txt, .csv, .xlsx, .pdf 等多種檔案類型。

內容提取： 根據檔案類型自動提取檔案中的文字或結構化數據。

AI 內容處理： 將提取的內容傳送給 AI 模型（透過 Langchain 和 OpenAI）進行進一步的分析、轉換或生成任務。

結果儲存： 將 AI 處理後的結果自動儲存到本地指定檔案。

Workflow 概覽
Workflow 的主要流程如下：

Local File Trigger: 監控指定的本地資料夾。

Edit Fields: 獲取觸發事件中的檔案路徑。

Read/Write Files from Disk: 讀取新檔案的內容。

Switch: 根據檔案的副檔名（txt, csv, xlsx, pdf）將流程分流。

Extract from File:

針對 .txt 檔案，提取文字內容。

針對 .csv, .xlsx, .pdf 檔案，分別提取其內容。

Aggregate (針對部分檔案類型): 匯總從 .csv, .xlsx, .pdf 提取的數據。

AI Agent (Langchain): 接收提取/匯總後的內容，並使用連接的語言模型進行處理。

OpenAI Chat Model: 提供 AI Agent 所需的語言模型能力（使用 gpt-4o-mini 模型）。

Convert to File: 將 AI Agent 的輸出轉換為檔案格式（例如純文字）。

Read/Write Files from Disk: 將最終結果寫入到指定的本地檔案。

技術堆疊
n8n: 自動化 workflow 平台。

n8n 內建節點: Local File Trigger, Set, Read/Write Files from Disk, Switch, Extract from File, Aggregate, Convert to File。

n8n 社群節點: AI Agent (Langchain), OpenAI Chat Model。

OpenAI API: 提供 AI 模型服務（需有效的 API 金鑰）。

Langchain: 用於連接 AI 模型。

環境建置與設定
要運行這個 workflow，你需要：

安裝並運行 n8n： 最推薦的方式是使用 Docker 或 Docker Compose。請參考 n8n 官方文件進行安裝：https://docs.n8n.io/getting-started/installation/

安裝必要的 n8n 節點： 這個 workflow 使用了 Langchain 和 OpenAI 節點。請在你的 n8n 實例中安裝 @n8n/n8n-nodes-langchain 節點。

獲取 OpenAI API 金鑰： 前往 OpenAI 網站獲取你的 API 金鑰。

設定 n8n 的 OpenAI Credentials： 在 n8n 介面中，新增一個 OpenAI 憑證，並填入你的 API 金鑰。請記住你為這個憑證設定的名稱（在 workflow JSON 中顯示為 "OpenAi account"，如果你更改了名稱，請更新 workflow 中的設定）。

設定監控資料夾和輸出檔案路徑：

Workflow 中的 Local File Trigger 節點目前設定監控路徑為 /Users/hayashi/Downloads/學員講義/素材/n8n/watch。請將其更改為你希望監控的本地資料夾路徑。

Workflow 中的最後一個 Read/Write Files from Disk 節點設定輸出檔案路徑為 /Users/hayashi/Downloads/學員講義/素材/n8n/resources/testj.txt。請將其更改為你希望儲存結果的檔案路徑。

導入 Workflow： 在你的 n8n 介面中，點擊左側選單的「Workflows」，然後點擊右上角的「Import from File」或「Import from JSON」按鈕，選擇導入你的 .json workflow 檔案。

使用方法
確保你的 n8n 實例正在運行，並且這個 workflow 已經被導入並啟用 (Active)。

將你想要處理的檔案（支援 .txt, .csv, .xlsx, .pdf）放入你在 Local File Trigger 節點中設定的監控資料夾。

n8n workflow 會自動被觸發，執行檔案讀取、內容提取、AI 處理等步驟。

AI 處理後的結果將會被儲存到你在最後一個 Read/Write Files from Disk 節點中設定的輸出檔案路徑。

環境變數 (.env)
雖然這個 workflow 的主要配置在 n8n 介面中完成，但如果你的 n8n 實例是透過 Docker Compose 運行，你可能需要在 .env 檔案中設定 OpenAI API 金鑰，然後在 n8n 中引用這個環境變數來設定憑證。

在你的 .env.example 檔案中（如果你創建了），應該包含類似以下的條目：

# 你的 OpenAI API 金鑰
# 請勿將實際金鑰直接提交到 Git！
OPENAI_API_KEY=你的實際金鑰會在這裡（請勿填寫）

然後在 n8n 的 OpenAI 憑證設定中，選擇使用「Environment Variable」並填入 OPENAI_API_KEY。

未來增強
支援更多檔案類型（例如 .docx, .pptx 等）。

增加錯誤處理機制，例如檔案讀取失敗或 AI 處理失敗時的通知。

將結果儲存到其他目的地，例如雲端儲存服務或發送郵件。

允許用戶透過 Webhook 上傳檔案來觸發 workflow。

增加更多 AI 處理選項或使用不同的 AI 模型。

授權
貢獻