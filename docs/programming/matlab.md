# MATLAB

- FFT绘制频谱图

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

- [本原多项式](https://baike.baidu.com/pic/%E6%9C%AC%E5%8E%9F%E5%A4%9A%E9%A1%B9%E5%BC%8F/3246261/1/3812b31bb051f819829acbddd0b44aed2f73e7ee?fr=lemma&fromModule=lemma_content-image#aid=1&pic=3812b31bb051f819829acbddd0b44aed2f73e7ee)（不可约多项式、生成多项式）

```matlab
>> primpoly(10)
 
Primitive polynomial(s) = 
 
D^10+D^3+1

ans =

        1033
```

- m序列

```matlab
function [pn] = mseq(coe)
    len = 2 ^ (length(coe) - 1)-1;
    pn = zeros(1, len);

    lfsr = [zeros(1, (length(coe)-2)) 1];
    for i = 1: len
        pn(i) = lfsr(end);
        lfsr_front = 0;
        for j = (length(coe) - 1): -1: 1
            lfsr_front = lfsr_front + coe(j + 1) * lfsr(j);
        end
        lfsr_front = mod(sum(lfsr_front), 2);
        lfsr = [lfsr_front lfsr(1: end - 1)];
    end
end
```

- 去频偏和相偏

```matlab
% 去频偏
acc = 2 ^ 24;
mf_dout2 = mf_dout .^ 2;
mf_dout2_fft = fft(mf_dout2, acc);
mf_dout2_fft_abs = abs(mf_dout2_fft.').^2;
mf_dout2_fft_abs = [mf_dout2_fft_abs(acc / 2 + 2 : acc), mf_dout2_fft_abs(1 : acc / 2 + 1)];
idx_max = find(mf_dout2_fft_abs == max(mf_dout2_fft_abs)) - acc / 2;
carrier = exp(-1j * 2 * pi * idx_max / acc * (1 : length(mf_dout)));
data_comp_down = mf_dout2.' .* carrier;

% 去相偏
phase_estm = atan2(sum(imag(data_comp_down)), sum(real(data_comp_down))) / 2;
carrier = exp(-1j * 2 * pi * idx_max / 2 / acc * (1 : length(mf_dout))) * exp(-1j * phase_estm);
mf_dout1 = mf_dout.' .* carrier;
```
