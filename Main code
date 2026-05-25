% PROJECT: MÔ PHỎNG HỆ THỐNG TÁCH KÊNH FDM
% SO SÁNH HIỆU NĂNG BỘ LỌC TĨNH (IIR, FIR) VÀ LỌC THÍCH NGHI (LMS)

clc; clear; close all;

%% 1. TẠO TÍN HIỆU FDM VÀ KÊNH TRUYỀN NHIỄU
fs = 10000;
t = 0:1/fs:1-1/fs;
N = length(t);

% 3 Kênh tín hiệu 
s1 = sin(2*pi*500*t);
s2 = sin(2*pi*1500*t); % Kênh mục tiêu cần tách
s3 = sin(2*pi*3000*t);

% Thêm nhiễu trắng ngẫu nhiên (Mô phỏng đường truyền xấu)
noise = 1.2 * randn(1, N);
x_rx = s1 + s2 + s3 + noise;

%% 2. THIẾT KẾ VÀ THỰC THI BỘ LỌC TĨNH (IIR & FIR)
f_cut = [1300 1700] / (fs/2);

% Phương án A: Bộ lọc IIR Butterworth (Bậc 6)
[b_iir, a_iir] = butter(6, f_cut, 'bandpass');
y_iir = filtfilt(b_iir, a_iir, x_rx);

% Phương án B: Bộ lọc FIR Cửa sổ Hamming (Bậc 100)
b_fir = fir1(100, f_cut, 'bandpass');
a_fir = 1;
y_fir = filtfilt(b_fir, a_fir, x_rx);

%% 3. THỰC THI BỘ LỌC THÍCH NGHI (LMS)
M_lms = 50;           % Bậc bộ lọc LMS
mu = 0.001;           % Bước nhảy hội tụ
% Sử dụng hàm sin() đồng pha với s2 để thuật toán hội tụ SNR tốt nhất
ref_signal = sin(2*pi*1500*t); 

w = zeros(M_lms, 1); 
y_lms = zeros(1, N); 
e_lms = zeros(1, N); 

% Zero padding cho tín hiệu tham chiếu
x_ref_padded = [zeros(1, M_lms-1), ref_signal];

for n = 1:N
    x_vec = x_ref_padded(n + M_lms - 1 : -1 : n)'; 
    y_lms(n) = w' * x_vec;          % Tín hiệu dự đoán
    e_lms(n) = x_rx(n) - y_lms(n);  % Sai số
    w = w + mu * e_lms(n) * x_vec;  % Cập nhật trọng số
end

%% 4. TÍNH TOÁN VÀ ĐÁNH GIÁ SNR
calc_snr = @(clean, recovered) 10*log10(var(clean) / var(clean - recovered));

snr_iir = calc_snr(s2, y_iir);
snr_fir = calc_snr(s2, y_fir);

% SNR của LMS (Chỉ tính sau khi thuật toán đã hội tụ, bỏ qua 1000 mẫu đầu)
start_idx = 1000; 
noise_lms = s2(start_idx:end) - y_lms(start_idx:end);
snr_lms = 10 * log10(var(s2(start_idx:end)) / var(noise_lms));

fprintf('\n=== KẾT QUẢ TỶ SỐ TÍN HIỆU TRÊN NHIỄU (SNR) ===\n');
fprintf('1. Bộ lọc IIR (Butterworth Bậc 6): %6.2f dB\n', snr_iir);
fprintf('2. Bộ lọc FIR (Hamming Bậc 100)  : %6.2f dB\n', snr_fir);
fprintf('3. Bộ lọc Thích nghi (LMS Bậc 50): %6.2f dB\n', snr_lms);
fprintf('===============================================\n\n');

%% 5. VẼ CÁC ĐỒ THỊ MINH CHỨNG
f_axis = (0:N/2-1)*(fs/N);
X_RX = abs(fft(x_rx))/N;
Y_IIR = abs(fft(y_iir))/N;
Y_FIR = abs(fft(y_fir))/N;

% --- Figure 1: Đặc tính tần số của Bộ lọc Tĩnh ---
figure('Name', 'Dac tinh Bo loc IIR vs FIR', 'Position', [100, 100, 700, 400]);
[H_iir, w_freq] = freqz(b_iir, a_iir, 1024, fs);
[H_fir, ~] = freqz(b_fir, a_fir, 1024, fs);
plot(w_freq, 20*log10(abs(H_iir)), 'b', 'LineWidth', 1.5); hold on;
plot(w_freq, 20*log10(abs(H_fir)), 'r', 'LineWidth', 1.5);
title('Đáp ứng biên độ của IIR (Bậc 6) và FIR (Bậc 100)');
xlabel('Tần số (Hz)'); ylabel('Biên độ (dB)');
xlim([0 4000]); ylim([-80 5]); grid on;
legend('IIR Butterworth', 'FIR Hamming');

% --- Figure 2: Phân tích phổ & Miền thời gian (IIR, FIR) ---
figure('Name', 'Ket qua Tach kenh FDM', 'Position', [150, 150, 900, 600]);
subplot(2,2,1); 
plot(f_axis, 2*X_RX(1:N/2), 'Color', [0.5 0.5 0.5]);
xlim([0 4000]); title('Phổ FFT tín hiệu thu (Nhiễu + 3 Kênh)'); xlabel('Hz');
subplot(2,2,2); 
plot(f_axis, 2*Y_IIR(1:N/2), 'b', 'LineWidth', 1.2); hold on;
plot(f_axis, 2*Y_FIR(1:N/2), 'r--', 'LineWidth', 1.2);
xlim([0 4000]); title('Phổ FFT Kênh 2 sau khi lọc'); legend('IIR', 'FIR'); xlabel('Hz');
subplot(2,2,3); 
plot(t(1:150), s2(1:150), 'k', 'LineWidth', 1.5); hold on;
plot(t(1:150), y_iir(1:150), 'b');
title(sprintf('IIR - Miền thời gian (SNR = %.1f dB)', snr_iir)); legend('Gốc', 'Khôi phục'); xlabel('s');
subplot(2,2,4); 
plot(t(1:150), s2(1:150), 'k', 'LineWidth', 1.5); hold on;
plot(t(1:150), y_fir(1:150), 'r');
title(sprintf('FIR - Miền thời gian (SNR = %.1f dB)', snr_fir)); legend('Gốc', 'Khôi phục'); xlabel('s');

% --- Figure 3: Khảo sát SNR FIR theo bậc ---
orders_fir = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100];
snr_fir_array = zeros(size(orders_fir));
for i = 1:length(orders_fir)
    b_temp = fir1(orders_fir(i), [1300 1700]/(fs/2), 'bandpass');
    y_temp = filtfilt(b_temp, 1, x_rx);
    snr_fir_array(i) = calc_snr(s2, y_temp);
end

figure('Name', 'Khao sat SNR FIR', 'Position', [200, 200, 700, 400]);
plot(orders_fir, snr_fir_array, '-o', 'LineWidth', 2); hold on;
yline(snr_iir, '--r', 'LineWidth', 2, 'DisplayName', 'IIR Butterworth (Bậc 6)');
grid on;
title('So sánh SNR: FIR (theo bậc) và IIR (Bậc 6 cố định)');
xlabel('Bậc bộ lọc FIR (Order)'); ylabel('SNR (dB)');
legend('FIR Hamming', 'IIR Butterworth (Bậc 6)', 'Location', 'best');

% --- Figure 4: Kết quả Lọc thích nghi LMS ---
figure('Name', 'Ket qua Loc Thich Nghi LMS', 'Position', [250, 250, 800, 700]);
subplot(3,1,1);
plot(e_lms.^2, 'b', 'LineWidth', 1);
title('Đường cong hội tụ của thuật toán LMS (Bình phương sai số)');
xlabel('Số mẫu lặp (n)'); ylabel('e^2(n)'); grid on;

subplot(3,1,2);
plot(t(start_idx:start_idx+150), s2(start_idx:start_idx+150), 'k', 'LineWidth', 2); hold on;
plot(t(start_idx:start_idx+150), y_lms(start_idx:start_idx+150), 'r--', 'LineWidth', 1.5);
title(sprintf('LMS - Miền thời gian (SNR = %.1f dB)', snr_lms));
xlabel('Thời gian (s)'); ylabel('Biên độ'); legend('Tín hiệu gốc s_2', 'Tín hiệu sau LMS'); grid on;

Y_LMS = abs(fft(y_lms))/N;
subplot(3,1,3);
plot(f_axis, 2*Y_LMS(1:N/2), 'r', 'LineWidth', 1.5);
title('Phổ tần số của tín hiệu sau khi lọc LMS');
xlabel('Tần số (Hz)'); ylabel('Biên độ');
xlim([0 4000]); grid on;
