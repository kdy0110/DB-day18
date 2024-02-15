# DB-day18
-- 단순 루프
DECLARE
 vn_num NUMBER := 2;
 vn_cnt NUMBER := 1;
BEGIN
 LOOP
   DBMS_OUTPUT.PUT_LINE(vn_num || '*' || vn_cnt || '=' || vn_num * vn_cnt);
   vn_cnt := vn_cnt + 1;
   EXIT WHEN vn_cnt >9; -- 단순루프는 무조건 탈출 조건이 있어야함.
 END LOOP;
END;
-- 구구단 출력 (중첩 loop는 loop ~ end loop 1개 (exit도 2개)
DECLARE
 i NUMBER := 2;
 j NUMBER := 1;
BEGIN
  LOOP
    j:=1;
    DBMS_OUTPUT.PUT_LINE(i || '단');
   LOOP
   DBMS_OUTPUT.PUT_LINE(i || '*' || j || '=' || i * j);
   j :=j + 1;
   EXIT WHEN j >9; -- 단순루프는 무조건 탈출 조건이 있어야함.
   END LOOP;
   i := i + 1;
   EXIT WHEN i >9;
 END LOOP;
END;
--- for 문
DECLARE
 dan NUMBER :=2;
BEGIN
 FOR i IN 1..9
 LOOP
  CONTINUE WHEN i=5;
   DBMS_OUTPUT.PUT_LINE(dan || '*' || i || '=' || (dan * i));
   END LOOP;
END;
-- 사용자 정의 함수
-- oracle 함수는 무조건 리턴값이 1개 있어야함.
CREATE OR REPLACE FUNCTION my_mod(num1 NUMBER, num2 NUMBER)
 RETURN NUMBER  -- 반환 타입 정의
IS
    vn_remainder NUMBER :=0; -- 반환할 나머지
    vn_quotient NUMBER :=0; -- 몫
BEGIN   
    vn_quotient := FLOOR (num1/num2);
    vn_remainder := num1 - (num2 * vn_quotient);
    RETURN vn_remainder; --  나머지를 반환
END;
-- 함수 실행
SELECT my_mod(4, 2)
     , mod(4, 2)
FROM dual;
-- 함수 삭제
DROP FUNCTION my_mod;

/* mem_id를 입력받아 등급을 리턴하는 함수를 만드시오
   ( VIP : 마일리지 5000이상
     GOLD : 마일리지 5000 미만 3000 이상
     SILVER : 나머지)
  매개변수 : mem_id 리턴값 : 등급(VARCHAR2)
*/
-- 함수 정의: 멤버 등급을 확인하는 함수
CREATE OR REPLACE FUNCTION fn_grade(p_mem_id VARCHAR2)
RETURN VARCHAR2
IS
   등급 VARCHAR2(20) := '';  -- 등급 변수 초기화
   mem_mlg NUMBER;  -- 멤버 마일리지 변수 선언
BEGIN
    -- 멤버 테이블에서 mem_id에 해당하는 멤버 마일리지를 가져옴
    SELECT mem_mileage
     INTO mem_mlg
    FROM member
    WHERE mem_id = p_mem_id;
    
    -- 멤버 마일리지에 따라 등급 결정
    IF mem_mlg > 5000 THEN
        등급 := 'VIP';
    ELSIF 3000 <= mem_mlg AND mem_mlg < 5000 THEN
        등급 := 'GOLD';
    ELSE
         등급 := 'SILVER';
    END IF; 
    
    -- 결정된 등급 반환
    RETURN 등급;
END;

-- 멤버 테이블에서 멤버의 이름, 마일리지, 등급을 조회하는 쿼리
SELECT mem_name, mem_mileage, fn_grade(mem_id)
FROM member;
