# TXT Merger – ブラウザで即TXTファイル結合ツール

**ここにTXTファイルをドロップするだけで、すぐに1つにまとめてダウンロードできます！**  
インストール・ダウンロード一切不要！このページだけで完結します

<style>
  body{font-family:"メイリオ",sans-serif;background:#f5f5f5;padding:20px;max-width:800px;margin:0 auto;}
  h1{text-align:center;color:#333;}
  #drop-area{border:4px dashed #666;border-radius:15px;padding:60px;background:#fff;margin:30px 0;cursor:pointer;transition:.3s;text-align:center;font-size:1.2em;}
  #drop-area.highlight{border-color:#007bff;background:#e7f3ff;}
  #file-list{background:white;padding:15px;border:1px solid #ddd;border-radius:8px;min-height:50px;max-height:300px;overflow:auto;margin:20px 0;}
  button{padding:12px 30px;font-size:16px;margin:5px;border:none;border-radius:6px;cursor:pointer;}
  #merge-btn{background:#007bff;color:white;}
  #clear-btn{background:#dc3545;color:white;}
  button:disabled{background:#999;cursor:not-allowed;}
  .info{background:#e9f7ff;padding:15px;border-radius:8px;margin:20px 0;font-size:0.9em;}
</style>

<div id="drop-area">ここにTXTファイルをドラッグ＆ドロップ<br>またはクリックして選択してください</div>
<input type="file" id="file-input" multiple accept=".txt,text/plain" style="display:none;">

<div id="file-list">選択されたファイル：なし</div>

<button id="merge-btn" disabled>結合してダウンロード</button>
<button id="clear-btn" disabled>選択をクリア</button>

<div class="info">
  100%このページ内だけで処理されます（サーバーにアップロードされません）<br>
  結合順＝選択した順番　｜　各ファイルの間に自動で改行が入ります
</div>

<script>
  const dropArea=document.getElementById('drop-area'),fileInput=document.getElementById('file-input'),
        fileList=document.getElementById('file-list'),mergeBtn=document.getElementById('merge-btn'),
        clearBtn=document.getElementById('clear-btn');let files=[];

  ['dragenter','dragover'].forEach(e=>dropArea.addEventListener(e,ev=>{ev.preventDefault();dropArea.classList.add('highlight');}));
  ['dragleave','drop'].forEach(e=>dropArea.addEventListener(e,ev=>{ev.preventDefault();dropArea.classList.remove('highlight');}));
  dropArea.addEventListener('drop',e=>{e.preventDefault();handleFiles(e.dataTransfer.files);});
  dropArea.addEventListener('click',()=>fileInput.click());
  fileInput.addEventListener('change',e=>handleFiles(e.target.files));
  clearBtn.onclick=()=>{files=[];updateList();};

  function handleFiles(newFiles){
    files=[...files,...Array.from(newFiles).filter(f=>f.type==='text/plain'||f.name.endsWith('.txt'))];
    updateList();
  }
  function updateList(){
    if(files.length===0){
      fileList.innerHTML='選択されたファイル：なし';
      mergeBtn.disabled=clearBtn.disabled=true;return;
    }
    let html='<strong>結合順（上から順）</strong><ol>';
    files.forEach(f=>html+=`<li>${f.name} (${(f.size/1024).toFixed(1)} KB)</li>`);
    html+='</ol>';fileList.innerHTML=html;
    mergeBtn.disabled=clearBtn.disabled=false;
  }

  mergeBtn.onclick=async()=>{
    let text='';
    for(const f of files){
      text+=await f.text();
      if(!text.endsWith('\n')) text+='\n';
    }
    const blob=new Blob([text],{type:'text/plain;charset=utf-8'});
    const a=document.createElement('a');
    a.href=URL.createObjectURL(blob);
    a.download='結合結果_'+new Date().toISOString().slice(0,19).replace(/:/g,'-')+'.txt';
    a.click();
  };
</script>

---

これで誰でもあなたのGitHubリポジトリを開いた瞬間に**そのまま使えます！**  
