**����Ч��ͼ**


![device-2017-08-09-141038222.gif](http://upload-images.jianshu.io/upload_images/2704327-03d89c4acd7dd247.gif?imageMogr2/auto-orient/strip)

**ΪʲôҪ���Զ���ؼ�?**
1���ض�����ʾ���
2���������е��û�����
3���Ż�����(���б���ͨ���Զ���ؼ�,ʵ�ָ��ӵ��Զ��岼��,����messure)
4����װ��

**����Զ���ؼ�**
1���Զ������Ե��������ȡ
2������onMeasrue
3������onLayout(ViewGroup)
4������onDraw
5��onTouchEvent
6��onInterceptTouchEnvent(ViewGroup)
7������״̬�Ļָ��뱣��(�˴���ֱ�Ӽ̳�Progress����View,���ô�0��ʼ)


**���濪ʼʵ��**

**1.1��ʽ���������ȡ**

1.1�½���ʽ(Ŀ����ʹ���Զ���View��ʱ�����ֱ����xml��������ֵ)
attr.xml

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <attr name="HorizontalProgresUnReachColor" format="color"></attr>
    <attr name="HorizontalProgresUnReachHeight" format="dimension"></attr>
    <attr name="HorizontalProgresReachColor" format="color"></attr>
    <attr name="HorizontalProgresReachHeight" format="dimension"></attr>
    <attr name="HorizontalProgresTextColor" format="color"></attr>
    <attr name="HorizontalProgresTextSize" format="dimension"></attr>
    <attr name="HorizontalProgresTextOffset" format="dimension"></attr>


    <declare-styleable name="CustomHorizontalProgresStyle">
        <attr name="HorizontalProgresUnReachColor" ></attr>
        <attr name="HorizontalProgresUnReachHeight" ></attr>
        <attr name="HorizontalProgresReachColor" ></attr>
        <attr name="HorizontalProgresReachHeight" ></attr>
        <attr name="HorizontalProgresTextColor" ></attr>
        <attr name="HorizontalProgresTextSize" ></attr>
        <attr name="HorizontalProgresTextOffset"></attr>

    </declare-styleable>

</resources>



```


1.2��ʽ�Ļ�ȡ

���췽���л�ȡ�Զ�������

```
 /**
     * ��ȡ�Զ�������
     */
    private void geStyleabletAttr(AttributeSet attrs) {
        TypedArray typedArray = getContext().obtainStyledAttributes(attrs, R.styleable.CustomHorizontalProgresStyle);
        HorizontalProgresUnReachColor = typedArray.getColor(R.styleable.CustomHorizontalProgresStyle_HorizontalProgresUnReachColor,DEAFUALT_PROGRESS_UNREACH_CORLOR);
        HorizontalProgresReachColor = typedArray.getColor(R.styleable.CustomHorizontalProgresStyle_HorizontalProgresUnReachColor,DEAFUALT_PROGRESS_REACH_CORLOR);
        //��sp��dpͳһת��Ϊsp
        HorizontalProgresReachHeight = typedArray.getColor(R.styleable.CustomHorizontalProgresStyle_HorizontalProgresReachHeight,dp2px(getContext(),DEAFUALT_PROGRESS_REACH_HEIGHH));
        HorizontalProgresUnReachHeight = typedArray.getColor(R.styleable.CustomHorizontalProgresStyle_HorizontalProgresUnReachHeight,dp2px(getContext(),DEAFUALT_PROGRESS_UNREACH_HEIGHH));
        HorizontalProgresTextColor = typedArray.getColor(R.styleable.CustomHorizontalProgresStyle_HorizontalProgresTextColor,DEAFUALT_PROGRESS_TEXT_CORLOR);
        HorizontalProgresTextSize = typedArray.getColor(R.styleable.CustomHorizontalProgresStyle_HorizontalProgresTextSize,DEAFUALT_PROGRESS_TEXT_SIZE);
        HorizontalProgresTextOffset = typedArray.getColor(R.styleable.CustomHorizontalProgresStyle_HorizontalProgresTextOffset,DEAFUALT_PROGRESS_TEXT_OFFSET);
        typedArray.recycle();//�ǵü����
    }
```


2.2.1��дonMeasure

```
 /**
     * Determines the width of this view
     * @param measureSpec A measureSpec packed into an int
     * @return The width of the view, honoring constraints from measureSpec
     */
    private int measureWidth(int measureSpec) {
        int result = 0;
        int specMode = MeasureSpec.getMode(measureSpec);
        int specSize = MeasureSpec.getSize(measureSpec);

        if (specMode == MeasureSpec.EXACTLY) {
            // We were told how big to be
            result = specSize;
        } else {
            // Measure the text
            result = dp2px(getContext(),DEAFUALT_PROGRESS_VIEW_WIDTH);//
            if (specMode == MeasureSpec.AT_MOST) {
                // Respect AT_MOST value if that was what is called for by measureSpec
                result = Math.min(result, specSize);
            }
        }

        return result;
    }
```
2.2.2 ������
```
 /**
     * Determines the width of this view
     * @param measureSpec A measureSpec packed into an int
     * @return The width of the view, honoring constraints from measureSpec
     */
    private int measureWidth(int measureSpec) {
        int result = 0;
        int specMode = MeasureSpec.getMode(measureSpec);
        int specSize = MeasureSpec.getSize(measureSpec);

        if (specMode == MeasureSpec.EXACTLY) {
            // We were told how big to be
            result = specSize;
        } else {
            // Measure the text
            result = dp2px(getContext(),DEAFUALT_PROGRESS_VIEW_WIDTH);//
            if (specMode == MeasureSpec.AT_MOST) {
                // Respect AT_MOST value if that was what is called for by measureSpec
                result = Math.min(result, specSize);
            }
        }

        return result;
    }


```

2.2.3 ����߶�
```
    /**
     * Determines the height of this view
     * @param measureSpec A measureSpec packed into an int
     * @return The height of the view, honoring constraints from measureSpec
     */
    private int measureHeight(int measureSpec) {
        int result = 0;
        int specMode = MeasureSpec.getMode(measureSpec);
        int specSize = MeasureSpec.getSize(measureSpec);

        if (specMode == MeasureSpec.EXACTLY) {
            // We were told how big to be
            result = specSize;
        } else {
            // Measure the text (beware: ascent is a negative number)
            //�˴��߶�Ϊ����Ľ��ȸ߶Ⱥ�δ����Ļ����Լ����ֵĸ߶ȵ����ֵ
            int textHeight = (int) (mPaint.descent() - mPaint.ascent());//�õ��ֵĸ߶��ж��ַ�ʽ,��һ����React,�ڶ������
            result = Math.max(textHeight,Math.max(HorizontalProgresReachHeight,HorizontalProgresUnReachHeight)) + getPaddingTop()
                    + getPaddingBottom();
            if (specMode == MeasureSpec.AT_MOST) {
                // Respect AT_MOST value if that was what is called for by measureSpec
                result = Math.min(result, specSize);
            }
        }
        return result;
    }
```

2.2.4 ��дonDraw
```
 @Override
    protected synchronized void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        canvas.save();//save��restore ͼ��ı���ͻع���صķ��� ��� http://blog.csdn.net/tianjian4592/article/details/45234419
        canvas.translate(0,getHeight()/2);//�ƶ�ͼ�㵽��ֱ����λ��
        float radio = getProgress()*1.0f/getMax();
        int textWidth = (int) mPaint.measureText(getProgress()+"%");//The width of the text
        float realWidth = getWidth() - getPaddingLeft() - getPaddingRight() - textWidth - HorizontalProgresTextOffset;//ʵ�ʿ�ȼ�ȥ���ֿ��
        float progressX  = radio * realWidth ;
        //��������Ľ�����
        mPaint.setColor(HorizontalProgresReachColor);
        mPaint.setStrokeWidth(HorizontalProgresReachHeight);
        //canvas.drawLine(getPaddingLeft(),getPaddingTop(),progressX,getPaddingTop(),mPaint);//ֱ�� ��ֱ��ͬһ�߶� float startY, float stopY һ��
        RectF mRectF = new RectF(getPaddingLeft(),getPaddingTop()-HorizontalProgresReachHeight/2,(int)progressX,HorizontalProgresReachHeight/2);//Բ�� int left, int top, int right, int bottom
        canvas.drawRoundRect(mRectF,15,15,mPaint);//Բ�Ǿ���
        //���ƽ���
        mPaint.setColor(HorizontalProgresTextColor);
        mPaint.setTextSize(HorizontalProgresTextSize);
        int y = (int) -((mPaint.descent() + mPaint.ascent())/2);//���־���
        canvas.drawText(getProgress()+"%",progressX + HorizontalProgresTextOffset/2,getPaddingTop() + y,mPaint);
        //����δ������Ľ���
        if (progressX + HorizontalProgresTextOffset + textWidth < getWidth() - getPaddingLeft() - getPaddingRight()){//����������,���ٻ�δ�����
            mPaint.setColor(HorizontalProgresUnReachColor);
            mPaint.setStrokeWidth(HorizontalProgresUnReachHeight);
            //canvas.drawLine(getPaddingLeft()+progressX + HorizontalProgresTextOffset + textWidth,getPaddingTop(),getWidth() - getPaddingLeft() - getPaddingRight() ,getPaddingTop(),mPaint);//��ֱ��ͬһ�߶� float startY, float stopY һ��
            RectF mRectF2 = new RectF(getPaddingLeft()+progressX + HorizontalProgresTextOffset + textWidth,getPaddingTop()-HorizontalProgresUnReachHeight/2,getWidth() - getPaddingLeft() - getPaddingRight(),HorizontalProgresUnReachHeight/2);//Բ�� int left, int top, int right, int bottom
            canvas.drawRoundRect(mRectF2,15,15,mPaint);//Բ�Ǿ���
        }
        canvas.restore();
    }

```

2.3.1�����ļ���ʹ��

```
  <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical" >

        <practice.csy.com.customprogress.view.CustomHorizontalProgres
            android:layout_marginTop="20dp"
            android:paddingLeft="10dp"
            android:paddingRight="10dp"
            android:id="@+id/horizontalProgress1"
            android:progress="0"
            app:HorizontalProgresReachColor="#FF0000"
            app:HorizontalProgresUnReachColor="#cab0b0"
            android:layout_width="200dp"
            android:layout_height="30dp">
        </practice.csy.com.customprogress.view.CustomHorizontalProgres>


        <practice.csy.com.customprogress.view.CustomHorizontalProgres
            android:paddingLeft="10dp"
            android:paddingRight="10dp"
            android:layout_marginTop="20dp"
            android:id="@+id/horizontalProgress2"
            android:progress="0"
            android:background="#ffffff"
            app:HorizontalProgresReachColor="#36513e"
            app:HorizontalProgresUnReachColor="#ffffff"
            android:layout_width="200dp"
            android:layout_height="30dp">

        </practice.csy.com.customprogress.view.CustomHorizontalProgres>

        <practice.csy.com.customprogress.view.CustomHorizontalProgres
            android:paddingLeft="10dp"
            android:paddingRight="10dp"
            android:layout_marginTop="20dp"
            android:id="@+id/horizontalProgress3"
            android:progress="0"
            app:HorizontalProgresReachColor="#00ffaa"
            app:HorizontalProgresUnReachColor="#0b1949"
            app:HorizontalProgresUnReachHeight="15dp"
            app:HorizontalProgresReachHeight="15dp"
            android:layout_width="200dp"
            android:layout_height="30dp">
        </practice.csy.com.customprogress.view.CustomHorizontalProgres>


    </LinearLayout>
```

2.3.2 �����и��½���

```
  timer = new Timer();
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                //ʵʱ���½���
                if (horizontalProgress1.getProgress() >= 100) {//ָ��ʱ��ȡ��
                    timer.cancel();
                }
                horizontalProgress1.setProgress(horizontalProgress1.getProgress()+1);
            }
        }, 50, 50);

        timer2 = new Timer();
        timer2.schedule(new TimerTask() {
            @Override
            public void run() {
                //ʵʱ���½���
                if (horizontalProgress2.getProgress() >= 100) {//ָ��ʱ��ȡ��
                    timer2.cancel();
                }
                horizontalProgress2.setProgress(horizontalProgress2.getProgress()+1);
            }
        }, 40, 40);


        timer3 = new Timer();
        timer3.schedule(new TimerTask() {
            @Override
            public void run() {
                //ʵʱ���½���
                if (horizontalProgress3.getProgress() >= horizontalProgress3.getMax()) {//ָ��ʱ��ȡ��
                    timer3.cancel();
                }
                horizontalProgress3.setProgress(horizontalProgress3.getProgress()+1);
            }
        }, 50, 50);
    }
```