# MATLAB

- 绘制频谱图

```matlab
plot(abs(fftshift(fft(  ))));  % fft
pwelch(  )                     % 功率谱密度
```

- 读取**CSV**文件

```matlab
f = 'iladata.csv';
data = csvread(f, 2, 5);  % 索引从0开始
i = data(:, 1);           % 索引从1开始
q = data(:, 2);           % 索引从1开始
s = i + 1j * q;
pwelch(s);
```

## Filter Design

- [`rcosdesign`](https://ww2.mathworks.cn/help/signal/ref/rcosdesign.html?searchHighlight=rcosdesign&s_tid=srchtitle_support_results_1_rcosdesign)

