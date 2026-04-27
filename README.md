# 🛡️ Dormant Mule Account Awakening Detection

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Data Engineering](https://img.shields.io/badge/Data%20Engineering-Star%20Schema-green)
![Fraud Detection](https://img.shields.io/badge/Domain-FinTech%20%2F%20Fraud-red)
![Status](https://img.shields.io/badge/Status-Project%20Concept-orange)

โครงการวิเคราะห์และพัฒนาระบบตรวจจับ **"การตื่นของบัญชีม้าผีดิบ" (Dormant Mule Awakening)** โดยใช้การวิเคราะห์พฤติกรรม (Behavioral Analytics) เพื่อสกัดกั้นการฟอกเงินที่มีความเสี่ยงสูงในระบบธนาคาร

## 📌 1. ความเป็นมาและความสำคัญ (Business Problem)
มิจฉาชีพในปัจจุบันมักใช้กลยุทธ์ **"บัญชีม้าผีดิบ"** ซึ่งเป็นบัญชีที่ถูกเปิดและทิ้งร้างไว้นานกว่า 1 ปีเพื่อหลบเลี่ยงระบบตรวจสอบความปลอดภัยสำหรับบัญชีเปิดใหม่ เมื่อถึงเวลาทำธุรกรรม บัญชีเหล่านี้จะถูก "ปลุก" ให้ตื่นขึ้นมาเพื่อรับโอนและยักย้ายถ่ายเทเงินจำนวนมากอย่างรวดเร็ว (**Hit & Run**) พฤติกรรมดังกล่าวหลุดรอดจากระบบ Rule-based แบบดั้งเดิม ส่งผลให้เกิดความเสียหายทางการเงินอย่างรุนแรงและกระทบต่อความเชื่อมั่นของสถาบันการเงิน

## 🎯 2. เป้าหมายโครงการ (SMART Objectives)
* **Specific:** ระบุโปรไฟล์บัญชีม้าที่ "เพิ่งตื่น" โดยใช้เกณฑ์ความเร็วการเคลื่อนย้ายเงิน (Dwell Time) เป็นตัวชี้วัดหลัก
* **Measurable:** เพิ่มอัตราการตรวจจับ (Detection Recall Rate) ให้ได้ **90%** และควบคุมอัตราการจับผิดตัว (False Positive) สำหรับลูกค้าปกติให้ต่ำกว่า **2%**
* **Time-bound:** พัฒนา ทดสอบ และจำลองระบบให้เสร็จสมบูรณ์ภายในไตรมาสที่ 4 ปี 2026

## 🏗️ 3. โครงสร้างข้อมูล (Data Architecture)
ระบบถูกออกแบบโดยใช้โครงสร้าง **Star Schema (Relational 3-Table Model)** เพื่อลดความซ้ำซ้อนและรองรับการวิเคราะห์พฤติกรรมในระดับลึก (Granular Analysis):

* **Transactions (Fact Table):** บันทึกธุรกรรม 10,000 รายการ (Transaction ID, Amount, Outflow Ratio, Dwell Time)
* **Accounts (Dimension Table):** โปรไฟล์ลูกค้า 3,645 ราย (Account ID, Age, Days Dormant)
* **Risk Labels (Dimension Table):** การจัดกลุ่มความเสี่ยงเพื่อการประเมินผล (Normal, Legitimate Awakening, Dormant Mule)

## 🧪 4. การเตรียมข้อมูลและควบคุมคุณภาพ (Data Engineering & QA)
เนื่องจากข้อมูลการทุจริตจริงของธนาคารเป็นความลับขั้นสูงสุด เราจึงพัฒนา **Synthetic Data Generator ด้วย Python** โดยมีการวางตรรกะที่รัดกุมดังนี้:

* **Behavioral Injection (การฝังพฤติกรรมเป้าหมาย):** สร้างสถานการณ์ข้อมูลไม่สมดุล (Class Imbalance) โดยให้มีบัญชีม้าเพียง **1.5%** ของข้อมูลทั้งหมด เพื่อจำลอง "การงมเข็มในมหาสมุทร"
* **Fraud-Aware Cleaning (การทำความสะอาดข้อมูลเชิงลึก):** พัฒนาระบบ QA ผ่าน Power Query ที่ยึดหลักการ *"การกรอง Error ต้องไม่ทำลายสัญญาณทุจริต"*
    * **ลบ (Purge):** System Errors หรือ Logical Errors เช่น ยอดเงินติดลบ หรืออายุลูกค้าเกิน 100 ปี
    * **เก็บ (Preserve):** พฤติกรรม Outliers เช่น การโอนเงินออก 100% ภายใน 1 นาที ซึ่งถือเป็น **"ร่องรอยของมิจฉาชีพ (Signal)"** ไม่ใช่ข้อมูลขยะ (Noise)
* **Data Transformation:** ใช้การแปลงค่าแบบ **Log Scale** เพื่อขยายให้เห็นการกระจายตัวของข้อมูลกลุ่ม 1.5% ได้ชัดเจนขึ้น

## 📊 5. ข้อมูลเชิงลึกที่ค้นพบ (Key Insights via 5W1H)
จากการทำ Exploratory Data Analysis (EDA) พบรูปแบบพฤติกรรมที่แยกแยะมิจฉาชีพออกจากผู้ใช้งานปกติได้อย่างสมบูรณ์ (**Linearly Separable**):

* 🕒 **WHEN (The Awakening Trigger):** อัตราความเสี่ยงจะพุ่งสูงขึ้นเป็น 60% ทันทีในกลุ่มบัญชีที่ถูกทิ้งร้างและไม่มีความเคลื่อนไหวนานเกิน **365 วัน**
* 💸 **WHAT (The Emptying Pattern):** เมื่อถูกปลุก บัญชีม้าจะโอนเงินออกเกือบ 100% (**Outflow Ratio > 0.95**) ทันทีที่เงินเข้า ซึ่งขัดแย้งกับพฤติกรรมการออมปกติ
* 👥 **WHO (Risk Demographics):** กลุ่มอายุ **20-35 ปี** คือกลุ่มเป้าหมายหลักที่มีการกระจุกตัวของบัญชีม้าสูงสุด (มักเกิดจากการรับจ้างเปิดบัญชี)
* ⚡ **HOW (The Velocity Paradox):** มิจฉาชีพใช้รูปแบบ **"ตีหัวเข้าบ้าน (Hit & Run)"** โดยมีเวลาเงินค้าง (Dwell Time) เพียง **15-20 นาที** ซึ่งเร็วกว่าลูกค้าปกติถึง 40 เท่า (800+ นาที)
* 🚨 **WHY (Value at Risk):** แม้บัญชีม้าจะมีจำนวนเพียง 1.5% แต่กลับเป็นกลุ่มที่มี **ยอดเงินสะสมหมุนเวียน (Sum Amount) สูงที่สุด** ในระบบ ถือเป็นช่องโหว่ความเสียหายที่รุนแรงที่สุด

## 🚀 6. กลยุทธ์การแก้ไขและสกัดกั้น (Strategic Recommendations)
เรานำเสนอแนวทางการป้องกันแบบลำดับขั้นอัตโนมัติ (**Automated Tiered Defense**) เพื่อรักษาสมดุลระหว่างความปลอดภัยและประสบการณ์ของลูกค้า:

1.  **Verify (ตรวจสอบ):** บังคับสแกนใบหน้า (Facial Biometrics) ทันที หากบัญชีที่หลับลึกเกิน 365 วันกลับมาเคลื่อนไหวด้วยยอดเงินที่สูงผิดปกติ
2.  **Hold (หน่วงเวลา):** ระบบทำการหน่วงเวลาการโอน (Fund Hold) 2 ชั่วโมงอัตโนมัติ สำหรับรายการที่ทำคำสั่งโอนออกมากกว่า 95% ของยอดเงินเข้า
3.  **Freeze (ระงับธุรกรรม):** ระงับการทำธุรกรรมอัตโนมัติ (Automated Freeze) หากตรวจพบพฤติกรรม Hit & Run ที่เงินค้างในบัญชี (Dwell Time) ต่ำกว่า 30 นาที

## 🛠️ 7. การติดตั้งและใช้งาน (Installation)

```bash
# Clone repository
git clone https://github.com/VeerapatLawprasert/dormant-mule-detection.git

# Install dependencies
pip install pandas numpy matplotlib
```

## 📈 8. แผนงานในอนาคต (Future Roadmap)
* **Phase 1: Real-World Telemetry:** เชื่อมต่อและประมวลผลข้อมูลจริงจากระบบ Core Banking (CBS)
* **Phase 2: Contextual Data Integration:** เชื่อมต่อพารามิเตอร์ด้านเทคนิค เช่น Geolocation, IP Address และ Device ID เพื่อเพิ่มมิติความแม่นยำ
* **Phase 3: Machine Learning Deployment:** พัฒนาและอัปเกรดโมเดลจาก Rule-based เป็นโมเดล AI (**Random Forest / XGBoost**) เพื่อทำ Real-time Risk Scoring แบบอัตโนมัติ 100%
