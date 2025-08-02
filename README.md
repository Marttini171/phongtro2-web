<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Quản lý phòng trọ</title>
  <style>
    body { font-family: Arial; max-width: 800px; margin: auto; padding: 20px; }
    input, select, button { margin: 5px; padding: 5px; }
    table, th, td { border: 1px solid #ccc; border-collapse: collapse; padding: 5px; }
  </style>
</head>
<body>
  <h2>Quản lý phòng</h2>
  <label>Mã phòng:</label><input id="maPhong"><br>
  <label>Tiền phòng:</label><input id="tienPhong"><br>
  <label>Tiền nước:</label><input id="tienNuoc"><br>
  <label>Tiền giặt:</label><input id="tienGiat"><br>
  <label>Tiền xe:</label><input id="tienXe"><br>
  <label>Dịch vụ khác:</label><input id="dichVu"><br>
  <label>Tủ lạnh:</label><input id="tuLanh"><br>
  <label>Chi phí khác:</label><input id="chiPhiKhac"><br>
  <label>Giá điện/kWh:</label><input id="giaDien"><br>
  <button onclick="themPhong()">Thêm phòng</button>
  <button onclick="suaPhong()">Sửa phòng</button>
  <button onclick="xoaPhong()">Xóa phòng</button>

  <h3>Ghi số điện</h3>
  <label>Chọn mã phòng:</label>
  <select id="chonPhongGhiDien"></select><br>
  <label>Tháng:</label><input id="thangGhi" placeholder="VD: 8"><br>
  <label>Số điện mới:</label><input id="soMoi"><br>
  <button onclick="ghiSoDien()">Ghi điện</button>

  <h3>In phiếu</h3>
  <label>Chọn mã phòng:</label><select id="chonPhongIn"></select>
  <label>Tháng:</label><input id="thangIn" placeholder="VD: 8"><br>
  <button onclick="inPhieu()">Tính tiền</button>
  <pre id="ketQua"></pre>

  <script>
    const dsPhong = {}; // lưu thông tin phòng
    const dienNang = {}; // lưu số điện theo tháng

    function themPhong() {
      const ma = document.getElementById('maPhong').value;
      dsPhong[ma] = {
        tienPhong: +document.getElementById('tienPhong').value,
        tienNuoc: +document.getElementById('tienNuoc').value,
        tienGiat: +document.getElementById('tienGiat').value,
        tienXe: +document.getElementById('tienXe').value,
        dichVu: +document.getElementById('dichVu').value,
        tuLanh: +document.getElementById('tuLanh').value,
        chiPhiKhac: +document.getElementById('chiPhiKhac').value,
        giaDien: +document.getElementById('giaDien').value
      };
      dienNang[ma] = dienNang[ma] || {}; // khởi tạo nếu chưa có
      capNhatSelect();
      alert('Đã thêm/sửa phòng');
    }

    function suaPhong() {
      themPhong(); // dùng chung hàm thêm vì nhập đè lên
    }

    function xoaPhong() {
      const ma = document.getElementById('maPhong').value;
      if (confirm(`Bạn có chắc muốn xóa phòng ${ma}?`)) {
        delete dsPhong[ma];
        delete dienNang[ma];
        capNhatSelect();
        alert('Đã xóa phòng');
      }
    }

    function capNhatSelect() {
      const selects = [document.getElementById('chonPhongGhiDien'), document.getElementById('chonPhongIn')];
      selects.forEach(sel => {
        sel.innerHTML = '';
        for (let ma in dsPhong) {
          const opt = document.createElement('option');
          opt.value = opt.text = ma;
          sel.add(opt);
        }
      });
    }

    function ghiSoDien() {
      const ma = document.getElementById('chonPhongGhiDien').value;
      const thang = +document.getElementById('thangGhi').value;
      const moi = +document.getElementById('soMoi').value;
      if (!dienNang[ma]) dienNang[ma] = {};
      dienNang[ma][thang] = moi;
      alert('Đã ghi điện');
    }

    function inPhieu() {
      const ma = document.getElementById('chonPhongIn').value;
      const thang = +document.getElementById('thangIn').value;
      const phong = dsPhong[ma];
      const soMoi = dienNang[ma]?.[thang] ?? 0;
      const soCu = dienNang[ma]?.[thang - 1] ?? 0;
      const soDien = soMoi - soCu;
      const tienDien = soDien * (phong?.giaDien || 0);

      const tong = phong.tienPhong + phong.tienNuoc + phong.tienGiat + phong.tienXe + phong.dichVu + phong.tuLanh + phong.chiPhiKhac + tienDien;

      document.getElementById('ketQua').textContent =
`PHIẾU TÍNH TIỀN - THÁNG ${thang}
Phòng: ${ma}
Số điện cũ (T${thang - 1}): ${soCu}
Số điện mới (T${thang}): ${soMoi}
Tiền điện: ${tienDien.toLocaleString()} đ
Tiền phòng: ${phong.tienPhong.toLocaleString()} đ
Nước: ${phong.tienNuoc.toLocaleString()} đ
Giặt: ${phong.tienGiat.toLocaleString()} đ
Xe: ${phong.tienXe.toLocaleString()} đ
Dịch vụ: ${phong.dichVu.toLocaleString()} đ
Tủ lạnh: ${phong.tuLanh.toLocaleString()} đ
Khác: ${phong.chiPhiKhac.toLocaleString()} đ
------------------------
TỔNG CỘNG: ${tong.toLocaleString()} đ`
    }
  </script>
</body>
</html>
