# 1.強化学習
## 1-1.要点まとめ
- **強化学習とは**

  長期的に報酬を最大化できるように環境のなかで行動を選択できるエージェントを作ることを目標とする機械学習の一分野。</br>
  行動の結果として与えられる利益（報酬）をもとに、行動を決定する原理を改善していく仕組み。</br>

- **強化学習の応用例**

  マーケティングの場合
  * 環境：会社の販売促進部
  * エージェント：プロフィールと購入履歴に基づいてキャンペーンメールを送る顧客を決めるソフトウェア。
  * 行動：顧客ごとに送信、非送信の二つの行動を選ぶことになる。
  * 報酬：キャンペーンのコストという負の報酬とキャンペーンで生み出される推測される売り上げという正の報酬を受ける。

- **探索と利用とトレードオフ**

  環境について事前に完璧な知識があれば、最適な行動を予測し決定することが可能だが、</br>
  強化学習の場合、不完全な知識を元に行動しながらデータを収集し、最適な行動を見つけていく。</br>

  そのとき、過去のデータで、ベストとされる行動のみを常にとり続ければ、他にもっとベストな行動を見つけることができない。</br>
  また、未知の行動のみをとり続ければ、過去の経験が活かせない。</br>
  これを探索と利用のトレードオフという。</br>

- **強化学習のイメージ**

  <img width="230" alt="image" src="https://user-images.githubusercontent.com/57135683/148182386-12b03e41-5dce-4cb2-adcf-af412bd40741.png"></br>
  方策関数と行動価値関数の二つを学習させる。

- **強化学習の歴史**

  関数近似法と、Q学習を組み合わせる手法が登場した。</br>
  * Q学習

    行動価値関数を、行動するたびに更新することにより学習を進める方法。</br>

  * 関数近似法

    価値関数や方策関数を関数近似する手法のこと。</br>

- **価値関数**

  価値関数を表す関数としては、状態価値関数と行動価値関数の２種類がある。</br>

  ある状態の価値に注目する場合は、状態価値関数。</br>
  状態と価値を組み合わせた価値に注目する場合は、行動価値関数。</br>


- **方策関数**

  方策ベースの強化学習手法において、</br>
  ある状態でどのような行動を採るのかの確率を与える関数のこと。</br>
  方策反復法でモデル化して最適化する。</br>

- **方策勾配法**

  <img src="https://latex.codecogs.com/svg.image?\theta^{(t&plus;1)}=\theta^{(t)}&plus;\varepsilon&space;\nabla&space;J\left(\theta\right)" title="\theta^{(t+1)}=\theta^{(t)}+\varepsilon \nabla J\left(\theta\right)" /></br>

  Jとは方策の良さ。これは定義しなければならない。</br>
  定義の方法は、**平均報酬**、**割引報酬**がある。</br>
  この定義に対応して、行動価値関数:Q(s,a)の定義を行う。</br>

</br>

## 1-2.確認テスト

> 教師あり、なし学習と強化学習の違い</br>

　教師なし、あり学習では、データに含まれるパターンを見つけ出す、およびそのデータから予測することが目標。</br>
　一方、強化学習では、優れた方策を見つけることが目標。</br>

</br>

## 1-3.参考図書
- 小川雄太郎『つくりながら学ぶ！深層強化学習　pyTorchによる実践プログラミング』株式会社マイナビ出版,2018.

  このような迷路を考え、これを方策勾配法でSTARTからGOALまでの最短距離を求める。</br>
  <img width="226" alt="image" src="https://user-images.githubusercontent.com/57135683/148728595-20c0fc07-0306-4fe0-96fa-f28de120c34f.png"></br>

  まず、初期の方策を決定するパラメータtheta_0を設定する。</br>
  ```code
  theta_0 = np.array([[np.nan, 1, 1, np.nan],#S0
                   [np.nan, 1, np.nan, 1], #S1
                   [np.nan, np.nan, 1, 1], #S2
                   [1, 1, 1, np.nan],      #S3
                   [np.nan, np.nan, 1, 1], #S4
                   [1, np.nan, np.nan, np.nan],#S5
                   [1, np.nan, np.nan, np.nan],#S6
                   [1, 1, np.nan, np.nan], #S7
                   ])
  ```

  次にsoftmax関数に従って、パラメータから方策に変換する。</br>
  ```code
  def softmax_convert_into_pi_from_theta(theta):
    beta = 1.0
    [m, n] = theta.shape
    pi = np.zeros((m, n))
    exp_theta = np.exp(beta * theta)
    
    for i in range(0, m):
        pi[i, :] = exp_theta[i, :] / np.nansum(exp_theta[i, :])
    
    pi = np.nan_to_num(pi)
    return pi

  #初期の方策pi_0
  pi_0 = softmax_convert_into_pi_from_theta(theta_0)
  ```
  実行結果:</br>
  <img width="285" alt="image" src="https://user-images.githubusercontent.com/57135683/148733540-0c72564c-2877-42ec-a7d4-922346d49a28.png"></br>

  この方策に従ってエージェントを行動させる。
  ```code
  def get_action_and_next_s(pi, s):
    direction = ["up", "right", "down", "left"]
    next_direction = np.random.choice(direction, p=pi[s, :])
    
    if next_direction == "up":
        action = 0
        s_next = s - 3
    elif next_direction == "right":
        action = 1
        s_next = s + 1
    elif next_direction == "down":
        action = 2
        s_next = s + 3
    elif next_direction == "left":
        action = 3
        s_next = s - 1
        
    return [action, s_next]

  def goal_maze_ret_s_a(pi):
    s = 0
    s_a_history = [[0, np.nan]]
    
    while (1):
        [action, next_s] = get_action_and_next_s(pi, s)
        s_a_history[-1][1] = action
        
        s_a_history.append([next_s, np.nan])
        
        if next_s == 8:
            break
        else:
            s = next_s
    return s_a_history
  ```

  その結果から、方策勾配法に従い方策を更新する。</br>
  <img src="https://latex.codecogs.com/svg.image?\theta_{s_i,&space;a_j}=\theta_{s_i,&space;a_j}&plus;\eta\cdot\Delta\theta_{s_i,&space;a_j}\\\Delta\theta_{s_i,&space;a_j}=\left\{N\left(s_i,&space;a_j\right)-P\left(s_i,&space;a_j\right)N\left(s_i,&space;a\right)\right\}/T" title="\theta_{s_i, a_j}=\theta_{s_i, a_j}+\eta\cdot\Delta\theta_{s_i, a_j}\\\Delta\theta_{s_i, a_j}=\left\{N\left(s_i, a_j\right)-P\left(s_i, a_j\right)N\left(s_i, a\right)\right\}/T" /></br>

  ```code
  def update_theta(theta, pi, s_a_history):
    eta = 0.1
    T = len(s_a_history) - 1
    
    [m, n] = theta.shape
    delta_theta = theta.copy()
    
    for i in range(0, m):
        for j in range(0, n):
            if not (np.isnan(theta[i, j])):
                SA_i = [SA for SA in s_a_history if SA[0] == i]
                
                SA_ij = [SA for SA in s_a_history if SA== [i, j]]
                
                N_i = len(SA_i)
                N_ij = len(SA_ij)
                delta_theta[i, j] = (N_ij - pi[i, j] * N_i) / T
    new_theta = theta + eta * delta_theta
    return new_theta
  ```
  
  これらを繰り返し行い最適な方策を求める。</br>
  ```code
  stop_epsilon = 10**-4

  theta = theta_0
  pi = pi_0

  is_continue = True
  count = 1
  while is_continue:
    s_a_history = goal_maze_ret_s_a(pi)
    new_theta = update_theta(theta, pi, s_a_history)
    new_pi = softmax_convert_into_pi_from_theta(new_theta)
    
    print(np.sum(np.abs(new_pi - pi)))
    print("迷路を解くのにかかったステップ数は"+str(len(s_a_history)-1) + "です")
    
    if np.sum(np.abs(new_pi - pi)) < stop_epsilon:
        is_continue = False
    else:
        theta = new_theta
        pi = new_pi
  ```
  <img width="419" alt="image" src="https://user-images.githubusercontent.com/57135683/148733892-10a397af-6d24-47a2-812e-da77b56acc56.png"></br>

</br>

# 2.AlphaGo
## 2-1.要点まとめ
　AlphaGoには、AlphaGo LeeとAlphaGo Zeroがある。</br>
- **AlphaGo Lee**

  * PolicyNet</br>
  <img width="400" alt="image" src="https://user-images.githubusercontent.com/57135683/148198176-3e1f7b33-595d-446e-8b2f-ba2a657b80a3.png"></br>
  CNNでできている方策関数。エージェントがどこに打つのが一番いいかの予測確率を出す。</br>
  
  * ValueNet</br>
  <img width="400" alt="image" src="https://user-images.githubusercontent.com/57135683/148198232-e49982e6-7197-4654-9676-9fdc29d9093f.png"></br>
  CNNでできている価値関数。現局面の勝率を出力する。</br>

  それぞれのチャンネルの情報は、</br>
  <img width="388" alt="image" src="https://user-images.githubusercontent.com/57135683/148199028-a226ede0-70c4-44ef-8697-5de8c0f0fd04.png"></br>

  学習は以下のステップで行われる。</br>
  1.教師あり学習によるRollOutPolicyとPolicyNetの学習</br>
  2.強化学習によるPolicyNetの学習</br>
  3.強化学習によるValueNetの学習</br>

- **AlphaGo Zero**

  AlphaGo Leeとの違いは、</br>
  1.教師あり学習を一切行わず、強化学習のみで作成。  
  2.特徴入力からヒューリスティックな要素を排除し、石の配置のみにした。  
  3.PolicyNetとValueNetを１つのネットワークに統合した。  
  4.ResidualNetを導入した。  
  5.モンテカルロ木探索からRollOutシミュレーションをなくした。  

  <img width="425" alt="image" src="https://user-images.githubusercontent.com/57135683/148202253-c313fe76-afc8-4a59-bd8b-c0b9fbd7e5b2.png"></br>

  ResidualNetworkとは、</br>
  <img width="397" alt="image" src="https://user-images.githubusercontent.com/57135683/148202538-a31a6f7c-4f03-4b14-9295-126e6950bd78.png"></br>
  ネットワークにショートカット構造を追加して、勾配の爆発、消失を抑える効果を狙ったもの。</br>
  
  AlphaGo Zeroの学習法は以下の3ステップ。</br>
  1.自己対局による教師データの作成

    現状のネットワークでモンテカルロ木探索を用いて自己対局を行う。</br>
    まず30手までランダムで打ち、そこから探索を行い勝敗を決定する。</br>
    自己対局中の各局面での着手選択確率分布と勝敗を記録する。</br>
    教師データの形は、（局面、着手選択確率分布、勝敗）が１セットとなる。</br>
    
  2.学習
    
    自己対局で作成した教師データを使い学習を行う。</br>
    NetworkのPolicy部分の教師に着手選択確率分布を用い、Value部分の教師に勝敗を用いる。</br>
    損失関数はPolicy部分はCrossEntropy、Value部分は平均二乗誤差。</br>
 
  3.ネットワークの更新

    学習後、現状のネットワークと学習後のネットワークとで対局テストを行い、</br>
    学習後のネットワークの勝率が高かった場合、学習後のネットワークを現状のネットワークとする。</br>

</br>

# 3.軽量化・高速化技術
## 3-1.要点まとめ
　深層学習は多くのデータを使用したり、パラメータ調整のために多くの時間をしようしたりするため、高速な計算が求められる。</br>
　複数の計算資源を使用し、並列的にニューラルネットを構成することで、効率の良い学習を行いたい。</br>

### 高速化
- **データ並列**

  親モデルを各ワーカーに子モデルとしてコピー。</br>
  データを分割し、各ワーカーごとに計算させる。</br>

  <img width="320" alt="image" src="https://user-images.githubusercontent.com/57135683/148633829-036b3696-8c27-4351-a0a8-37d64843be48.png"></br>

  データ並列化は各モデルのパラメータの合わせ方で、同期型か非同期型か決まる。</br>
  * 同期型  

    <img width="320" alt="image" src="https://user-images.githubusercontent.com/57135683/148633859-665a8620-b5df-43bc-bede-38a55b5496ad.png"></br>
    各ワーカーの計算が終わるのを待ち、全ワーカーの勾配が出たところで勾配の平均を計算し、親モデルのパラメータを更新する。</br>

  * 非同期型

    <img width="327" alt="image" src="https://user-images.githubusercontent.com/57135683/148633877-32b88db0-128f-4ebb-91b6-e295a000fee2.png"></br>
    
    各ワーカーはお互いの計算が終わるのを待たず、各子モデルごとに更新を行う。</br>
    学習が終わったら子モデルはパラメータサーバーにPushされる。</br>
    新に学習を始めるときは、パラメータサーバからPopしたモデルに対して学習していく。</br>
  
    非同期型の方が処理は早いが、最新モデルのパラメータが利用できないので学習が不安定になりやすいので、現在は同期型のほうが主流。</br>
 
- **モデル並列**

  親モデルを各ワーカーに分割し、それぞれのモデルを学習させる。</br>
  全てのデータで学習が終わった後で、一つのモデルに復元。</br>
  <img width="364" alt="image" src="https://user-images.githubusercontent.com/57135683/148634206-674bd1fd-9fec-49e3-88b8-de78776815c8.png"></br>

  モデルが大きいときはモデル並列かを、データが大きいときはデータ並列化をするとよい。</br>
  モデルのパラメータが多いほど、スピードアップの効率も向上する。</br>

- **GPU**

  * GPGPU
    元々の使用目的であるグラフィック以外の用途で使用できるGPUの総称。</br>
    
    + CUDA

      GPU上で並列コンピューティングを行うためのプラットフォーム。</br>
      NVIDIA社が開発しているGPUのみで使用可能。</br>
      Deep Learning用に提供されている。</br>
      
    + OpenCL

      オープンな並列コンピューティングのプラットフォーム。</br>
      NVIDIA社以外の会社のCPUからでも仕様可能。</br>
      Deep Learning用の計算に特化しているわけではない。</br>
    
  * CPU

    高性能なコアが少数。複雑で連続的な処理が得意。</br>
  
  * GPU

    比較的低性能なコアが多数。簡単な並列処理が得意。</br>
    ニューラルネットの学習は単純な行列演算が多いので高速化が可能。</br>

### 軽量化
- **量子化**

  ネットワークが大きくなると大量のパラメータが必要となり学習や推論に多くのメモリと演算処理が必要なので、</br>
  通常のパラメータの64bit浮動小数点を32bitなど下位の精度に落とすことでメモリと演算処理の削減を行う。</br>
  
  * 利点

    計算の高速化、省メモリ化</br>
    
  * 欠点

    精度の低下</br>
  
- **蒸留**

  規模の大きなモデルの知識を使い、軽量なモデルの作成を行う。</br>
  
  学習済みの精度の高いモデルの知識を軽量なモデルへ継承させる。</br>
  <img width="314" alt="image" src="https://user-images.githubusercontent.com/57135683/148335282-cf5b3546-6f93-4a06-9c19-12ad859fffd1.png"></br>

  蒸留は教師モデルと生徒モデルの２つで構成される。</br>
  * 教師モデル

    予測精度の高い、複雑なモデルやアンサンブルされたモデル。
    
  * 生徒モデル

    教師モデルをもとに作られる軽量なモデル。
    
  教師モデルの重みを固定し、生徒モデルの重みを更新していく。</br>
  誤差は教師モデルと生徒モデルのそれぞれの誤差を使い、重みを更新していく。</br>
  <img width="222" alt="image" src="https://user-images.githubusercontent.com/57135683/148634499-d7dc2ea1-3fcd-4539-83f7-e80469518fea.png"></br>
  
   蒸留によって少ない学習回数でより精度の良いモデルを作成することができる。</br>

- **プルーニング**

  モデルの精度に寄与が少ないニューロンを削減することでモデルの軽量化、高速化が見込まれる。</br>
  <img width="195" alt="image" src="https://user-images.githubusercontent.com/57135683/148335875-7472894d-ff90-45fa-9510-e47f30965044.png"></br>

  ニューロンの削減手法は重みが閾値以下の場合ニューロンを削減し、再学習を行う。</br>

</br>

# 4.応用モデル
## 4-1.要点まとめ
- **MobileNet**

  ディープラーニングモデルの軽量化・高速化・高精度化を実現。</br>
  一般的な畳み込みレイヤーは計算量が多いので、Depthwise ConvolutionとPointwise Convolutionを組み合わせて軽量化を実現。</br>
  
  * Depthwise Convolution

    入力マップのチャンネルごとに畳み込みを実施。</br>
    出力マップをそれらと結合。</br>
    
  * Pointwise Convolution
    
    入力マップのポイントごとに畳み込みを実施。</br>
    出力マップはフィルタ数分だけ作成可能。</br>

- **DenseNet**

  畳み込みニューラルネットワークアーキテクチャの一種。</br>
  ニューラルネットワークは層が深くなるにつれて、学習が難しくなったが、DenseNetではDenseBlockを用いてその問題に対処した。</br>

- **BatchNorm**

  レイヤー間を流れるデータの分布を、ミニバッチ単位で平均が０、分散が１になるように正規化。</br>
  学習時間の短縮や初期値への依存低減、過学習の抑制など効果がある。</br>

  * 問題点
    BatchSizeが小さい条件下では、学習が収束しないことがある。</br>
  
  他にも、**LayerNet正規化**、**Instances正規化**がある。
 
- **Wavenet**

  音声生成モデル。</br>
  PixelCNN（高解像度の画像を精密に生成できる手法）を音声に応用したもの。</br>
  
  時系列データに対して畳み込み（Dilated convolution）を適用する。</br>  
  * Dilated convolution

    層が深くなるにつれて畳み込みリンクを離す。</br>
    受容野を簡単に増やすことができるという利点がある。</br>
 
    <img width="429" alt="image" src="https://user-images.githubusercontent.com/57135683/148635141-05fd1f32-c986-4bfb-9629-d5b834f3d266.png"></br>

</br>

## 4-2.確認問題
> Depthwise Convolutionはチャンネルごとに空間方向へ畳み込む。すなわち、
> チャンネルごとにD_k x D_k x 1のサイズのフィルターをそれぞれ用いて計算を行うため、
> その計算量は（い）となる。

　<img src="https://latex.codecogs.com/svg.image?&space;H&space;\times&space;&space;W&space;\times&space;C&space;\times&space;K&space;\times&space;K" title=" H \times W \times C \times K \times K" /></br>

</br>

> 次にDepthwise Convolutionの出力をPointwise Convolutionによってチャンネル方向に
> 畳み込む。すなわち、出力チャンネル毎に1x1xMサイズのフィルターをそれぞれ用いて計算を
> 行うため、その計算量は（う）となる。

　<img src="https://latex.codecogs.com/svg.image?H&space;\times&space;W&space;\times&space;C&space;\times&space;M" title="H \times W \times C \times M" /></br>

</br>

> 深層学習を用いて結合確率を学習する際に、効率的な学習が行えるアーキテクチャが提案されたことがWaveNetの大きな貢献の１つである。</br>
> 提案された新しいConvolution型アーキテクチャは、</br>
> （あ）と呼ばれ、結合確率を効率的に学習できるようになっている。

　Dilated causal convolution
  
</br>

> Dilated causal convolutionを用いた際の大きな利点は、単純なConvolution layerと比べて（い）ことである。

　パラメータ数に対する受容野が広い。

</br>

# 5.Transformer
## 5-1.要点まとめ
  文章を翻訳するモデルであるSeq2seq（RNNベースのEncoder-Decoderモデル）は、文長が長くなると翻訳精度が落ちたため、Attentionメカニズムを用いていた。
  TransformerはRNNやCNNを用いずAttentionのみを用いたモデルである。</br>
  
## 5-2.実装演習

訓練</br>
```code
# 訓練
best_valid_bleu = 0.

for epoch in range(1, num_epochs+1):
    start = time.time()
    train_loss = 0.
    train_refs = []
    train_hyps = []
    valid_loss = 0.
    valid_refs = []
    valid_hyps = []
    # train
    for batch in train_dataloader:
        batch_X, batch_Y = batch
        loss, gold, pred = compute_loss(
            batch_X, batch_Y, model, criterion, optimizer, is_train=True
            )
        train_loss += loss
        train_refs += gold
        train_hyps += pred
    # valid
    for batch in valid_dataloader:
        batch_X, batch_Y = batch
        loss, gold, pred = compute_loss(
            batch_X, batch_Y, model, criterion, is_train=False
            )
        valid_loss += loss
        valid_refs += gold
        valid_hyps += pred
    # 損失をサンプル数で割って正規化
    train_loss /= len(train_dataloader.data) 
    valid_loss /= len(valid_dataloader.data) 
    # BLEUを計算
    train_bleu = calc_bleu(train_refs, train_hyps)
    valid_bleu = calc_bleu(valid_refs, valid_hyps)

    # validationデータでBLEUが改善した場合にはモデルを保存
    if valid_bleu > best_valid_bleu:
        ckpt = model.state_dict()
        torch.save(ckpt, ckpt_path)
        best_valid_bleu = valid_bleu

    elapsed_time = (time.time()-start) / 60
    print('Epoch {} [{:.1f}min]: train_loss: {:5.2f}  train_bleu: {:2.2f}  valid_loss: {:5.2f}  valid_bleu: {:2.2f}'.format(
            epoch, elapsed_time, train_loss, train_bleu, valid_loss, valid_bleu))
    print('-'*80)
```
<img width="541" alt="image" src="https://user-images.githubusercontent.com/57135683/148717418-e9baf148-6544-4722-a779-7dde0bafdebd.png">

</br>

評価</br>
```code
# BLEUの評価
test_dataloader = DataLoader(
    test_X, test_Y, 128,
    shuffle=False
    )
refs_list = []
hyp_list = []

for batch in test_dataloader:
    batch_X, batch_Y = batch
    preds, *_ = test(model, batch_X)
    preds = preds.data.cpu().numpy().tolist()
    refs = batch_Y[0].data.cpu().numpy()[:, 1:].tolist()
    refs_list += refs
    hyp_list += preds
bleu = calc_bleu(refs_list, hyp_list)
print(bleu)
```
<img width="125" alt="image" src="https://user-images.githubusercontent.com/57135683/148718943-3b3eaf62-dd20-468a-96df-26f432cbc5ad.png"></br>

</br>

# 6.物体検知・セグメンテーション
## 6-1.要点まとめ
　物体検知とは、画像に対し、どこに何があるかを検出するタスク。</br>
　分類、物体検知、意味領域分割、個体領域分割のタスクがある。</br>

　代表的なデータセットにVOC12、ILSVRC17、MS COCO18、OICOD18などがある。</br>
　タスクに応じて適切なデータセットを使う必要がある。</br>

　物体検出においてはクラスラベルだけでなく、物体位置の予測精度も評価したい。</br>
　それを測るための指標として、IoUがある。</br>

　<img width="168" alt="image" src="https://user-images.githubusercontent.com/57135683/148365016-31df6108-4d06-4bee-8f13-80320a4c0c74.png"></br>
　<img src="https://latex.codecogs.com/svg.image?IoU=\frac{TP}{TP&plus;FP&plus;FN}" title="IoU=\frac{TP}{TP+FP+FN}" /></br>

　このIoUを用いて、Precision、Recallを求める。</br>
　まずクラスを固定して、confの値を変化させ、そのそれぞれに対して求めたPrecisionとRecallをもとめ、</br>
　求められたPR曲線の下側面積をAverage Precisionとする。</br>
　次にそれをクラス数で割った指標がmAPdであり、これを用いて検出精度を評価する。</br>

　また、応用上の要請から、検出精度に加え検出速度も問題となる。</br>
　検出速度を求める指標として、1sに何枚のフレームを処理できるかを表すFPS、1枚のフレームを何秒で処理できるかを表すinference timeなどがある。</br>
　<img width="308" alt="image" src="https://user-images.githubusercontent.com/57135683/148365856-6f72b006-652f-4f74-8748-5e36e6f3356b.png">
　<img width="235" alt="image" src="https://user-images.githubusercontent.com/57135683/148365903-e6f1a79f-50f7-4696-8379-9cc9e794894e.png"></br>

　物体検出のフレームワークには大きく分けて、２段階検出器と１段階検出器がある。</br>
　２段階検出器は候補領域の検出とクラス推定を別々に行う。</br>
　１段階検出器は候補領域の検出とクラス推定を同時に行う。SSD、YOLOなどがある。</br>
