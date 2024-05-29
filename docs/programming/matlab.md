# MATLAB

> - [awgn](https://www.mathworks.com/help/comm/ref/awgn.html)
> - [berawgn](https://www.mathworks.com/help/comm/ref/berawgn.html?searchHighlight=berawgn&s_tid=srchtitle_support_results_1_berawgn)
> - [histogram](https://www.mathworks.com/help/matlab/ref/matlab.graphics.chart.primitive.histogram_zh_CN.html)
> - [kron](https://www.mathworks.com/help/matlab/ref/kron.html?lang=en)

- FFT 绘制频谱图

```matlab
N = ; fs = ;  %FIXME
f = (-fs/2 : fs/N : fs/2-fs/N);
plot(f, 10 * log10(abs(fftshift(fft( , N)))));
pwelch( )  % 功率谱密度
freqz( )  % 幅相函数
```

- 写 `.txt` 文件

```matlab
fid = fopen('data.txt','wt');
fprintf(fid, '%g\n', data);
fclose(fid);
```

- 测试 GPU 程序

```matlab
close all; clear; clc;

tic;

fs = 160e6;
Rs = 5e6; Ts = 1 / Rs;
Rb = Rs; 
fc = 0.25 * fs;
usmp_rate = fs / Rs;

beta = 0.35; span = 32; sps = usmp_rate;
hrc = rcosdesign(beta, span, sps, 'sqrt');

figure;
subplot(1, 2, 1);
pwelch(hrc);
subplot(1, 2, 2);
plot(20 * log10(abs(fftshift(fft(hrc, 2048)))));

figure;
freqz(hrc);

num = 10e5;
data = randi([0 1], 1, num);
data = gpuArray(data);
data_bipolar = real(exp(1j * pi * data));
baseband = conv(upsample(data_bipolar, usmp_rate), hrc);
delay = (length(hrc) - 1 ) / 2;
baseband = baseband(delay + 1:end - delay);

baseband_cpu = gather(baseband);
figure;
subplot(1, 2, 1);
pwelch(baseband_cpu, [], [], 2048, fs);
subplot(1, 2, 2);
plot(20 * log10(abs(fftshift(fft(baseband, 2048)))));

total_t = Ts * num;
t = 0 : 1 / fs : (total_t - 1 / fs);
t = gpuArray(t);
carrier = exp(1j * 2 * pi * fc * t);
carrier_cpu = gather(carrier);
figure;
subplot(1, 2, 1);
pwelch(carrier_cpu, [], [], 2048, fs);
subplot(1, 2, 2);
carrier_fft = fftshift(fft(carrier, 2048));
plot(20 * log10(abs(carrier_fft)));

modulated_signal = baseband .* carrier;
modulated_signal_cpu = gather(modulated_signal);
figure;
subplot(1, 2, 1);
pwelch(modulated_signal_cpu, [], [], 2048, fs);
subplot(1, 2, 2);
plot(20 * log10(abs(fftshift(fft(modulated_signal, 2048)))));

toc;
```

- 读取 **CSV** 文件

```matlab
f = 'iladata.csv';
data = csvread(f, 2, 5);  % 索引从0开始
i = data(:, 1);           % 索引从1开始
q = data(:, 2);           % 索引从1开始
s = i + 1j * q;
pwelch(s);
plot(10 * log10(abs(fftshift(fft(s)))));
pxx_s = 10 * log10(sum(abs(s)));
```

- [本原多项式](https://baike.baidu.com/pic/%E6%9C%AC%E5%8E%9F%E5%A4%9A%E9%A1%B9%E5%BC%8F/3246261/1/3812b31bb051f819829acbddd0b44aed2f73e7ee?fr=lemma&fromModule=lemma_content-image#aid=1&pic=3812b31bb051f819829acbddd0b44aed2f73e7ee)（不可约多项式、生成多项式）

```matlab
>> primpoly(10)
 
Primitive polynomial(s) = 
 
D^10+D^3+1

ans =

        1033
```

- m 序列

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

- 计算信号功率

```matlab
signal_power = sum(abs(signal(:)) .^ 2) / length(signal(:));
```

- 串口

```matlab
close all; clear; clc;

ports = serialportlist;

if isempty(ports)
    disp("No serial device is detected!");
else
    for i = 1:length(ports)
        fprintf('Port%d: %s\n', i, ports(i));
    end
end

serial_com_name = "";
serial_baud_rate = 115200;
serial_data_bit = 8; serial_stop_bit = 1; serial_check_bit = "none";

try
    serial_obj = serialport(serial_com_name, serial_baud_rate, "DataBits", serial_data_bit, "Parity", serial_check_bit, "StopBits", serial_stop_bit, "Timeout", 1);
    disp("Serial port connected")
catch
    disp("Serial port connection failed")
    delete(serial_obj);
end

%% Test
test_str = "Hello World!\r\n";
write(serial_obj, test_str, "uint8");

test_read_bytes = 100;
recv_data = read(serial_obj, test_read_bytes, "char");
recv_str = char(recv_data);

disp("Received Data: ");
disp(recv_str);

clear serial_obj;
```

- 模拟噪声

```matlab
%% Band Limited White Noise
% White Noise
N = 65536;
noise_power_dBm = -40;
noise_power_W = 10 ^ (noise_power_dBm / 10 - 3);
noise_var = noise_power_W * (1e6);
noise = sqrt(noise_var) * randn(N, 1);

% Band Limited White Noise
f_low = 1500e6; f_high = 2500e6;
fs = 3 * f_high;
order = 10;
[b, a] = butter(order, [f_low f_high] / (fs / 2), 'bandpass');
filtered_noise = filter(b, a, noise);

f_noise = (0:(N - 1)) * (fs / N);
P_noise = 20 * log10(abs(fft(filtered_noise) / N));

figure;
plot(f_noise, P_noise);
axis([0 fs/2 -90 0]);
xlabel('Frequency (Hz)'); ylabel('Amplitude (dB)');
title('Spectrum of Band Limited White Noise');
```

- DDS 量化性能的仿真

```matlab
close all; clear; clc;

tic;

% FIXME System Parameters
fs = 160e3;
f_out = 10e3;                           % f_out < 80e3
phase_width = 16; N = 2 ^ phase_width;  % Phase width quantization (frequency resolution = fs / N;)
output_width = 16;                      % Magnitude quantization
phase_offset = 0;
phase_increment = f_out * N / fs;

n = 0 : 1/N : 1/4 - 1/N;
cos_rom = cos(2 * pi * n);
len_lut = length(cos_rom);

figure;
stem(cos_rom(1:len_lut));
xlabel("Points"); ylabel("Amplitude");
xlim([0, len_lut]); ylim([0, 1]);
title("Look-up Table (1/4 cos)"); grid on;


t_total = 1;
t = 0 : 1/fs : t_total - 1/fs;
dds_dout_len = length(t);

dds_dout = zeros(1, dds_dout_len);
phase_acc = phase_offset;

for i = 1:dds_dout_len
    idx = floor(mod(phase_acc, N));
    if idx == 0
        dds_dout(i) = 1;
    elseif idx > 0 && idx <= len_lut
        idx_new = idx;
        dds_dout(i) = 1 * cos_rom(idx_new);
    elseif idx > len_lut && idx <= len_lut * 2
        idx_new = 2 * len_lut + 1 - idx;
        dds_dout(i) = -1 * cos_rom(idx_new);
    elseif idx > len_lut * 2 && idx <= len_lut * 3
        idx_new = idx - 2 * len_lut;
        dds_dout(i) = -1 * cos_rom(idx_new);
    else
        idx_new = 4 * len_lut + 1 - idx;
        dds_dout(i) = 1 * cos_rom(idx_new);
    end
    phase_acc = phase_acc + phase_increment;
end

dds_dout = floor(dds_dout / max(dds_dout) * 2 ^ (output_width - 1));

figure;
subplot(2, 1, 1);
plot(t(1:2048), dds_dout(1:2048));
xlabel('Time (s)'); ylabel('Amplitude');
title('DDS Generated Signal'); grid on;
subplot(2, 1, 2);
pwelch(dds_dout);

sgtitle("Simulation of the Quantization Performance of DDS");

toc;
```
