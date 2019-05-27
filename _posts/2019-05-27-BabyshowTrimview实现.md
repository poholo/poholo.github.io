---
layout:     post
title:      "BabyshowTrimview实现"
subtitle:   " \"比特币$8800\""
date:       2019-05-27 11:09:00
author:     "littleplayer"
header-img: "img/about-bg.jpg"
catalog: true
tags:
    - 视频剪辑
    - VideoTrimView
    - Babyshow
---

> Babyshow是工程名称，线上叫做晒娃，此款App是17年春季左右上线的，却郁郁不得志。看到其中代码，百味回转。

# TirmView实现效果

[视频地址](https://www.iesdouyin.com/share/video/6695304122083593480/?region=CN&mid=6695409443535948552&u_code=15hc71kg4&titleType=title&timestamp=1558925952&utm_campaign=client_share&app=aweme&utm_medium=ios&tt_from=copy&utm_source=copy)

# 类图
![BabyShowTrimView](/img/post/babyshow_trimview.png)

# code
[代码地址](https://github.com/poholo/iOSDemoTest/tree/dev/MCTestProjectDemo/Code/Demo/AVFoundation/TirmView/)
```objc
#import <UIKit/UIKit.h>


typedef void(^BabyVideoCropBottomTrimChangeBlock)(CGFloat progress);

@interface BabyVideoCropBottomTrimView : UIView

@property(nonatomic, readonly) UICollectionView *collectionView;

@property(nonatomic, assign) CGFloat limitMiniRate;

@property(nonatomic, assign) CGFloat limitMaxRate;

@property(nonatomic, copy) BabyVideoCropBottomTrimChangeBlock leftVideoCropBottomTrimChangeBlock;
@property(nonatomic, copy) BabyVideoCropBottomTrimChangeBlock rightVideoCropBottomTrimChangeBlock;

@property(nonatomic, copy) BabyVideoCropBottomTrimChangeBlock leftVideoCropBottomTrimEndBlock;
@property(nonatomic, copy) BabyVideoCropBottomTrimChangeBlock rightVideoCropBottomTrimEndBlock;

@property(nonatomic, copy) BabyVideoCropBottomTrimChangeBlock trimInBlock;
@property(nonatomic, copy) BabyVideoCropBottomTrimChangeBlock trimOutBlock;

- (void)loadImages:(NSArray<NSString *> *)images;

- (void)loadDefaultTrim:(CGFloat)startPosDuration duration:(CGFloat)duration;

@end
```


```objc

#import "BabyVideoCropBottomTrimView.h"

#import "ThumbCollectionCell.h"
#import "ThumbImageView.h"

@interface BabyVideoCropBottomTrimView () <UICollectionViewDelegate, UICollectionViewDataSource>

@property(nonatomic, strong) UICollectionView *collectionView;

@property(nonatomic, strong) ThumbImageView *leftImageView;
@property(nonatomic, strong) ThumbImageView *rightCoverImageView;
@property(nonatomic, strong) UIView *leftCoverView;
@property(nonatomic, strong) UIView *rightCoverView;

@property(nonatomic, strong) NSArray<NSString *> *images;


@property(nonatomic, assign) CGFloat controlSpace;
@property(nonatomic, assign) CGFloat handleWidth;

@end


@implementation BabyVideoCropBottomTrimView {

}

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        [self configDefault];
        [self createViews];
        [self addEvent];
    }
    return self;
}

- (void)loadImages:(NSArray<NSString *> *)images {
    self.images = images;
    [self.collectionView reloadData];
}

- (void)loadDefaultTrim:(CGFloat)startPosDuration duration:(CGFloat)duration {
    CGPoint leftTransationPoint = CGPointMake(CGRectGetWidth(self.collectionView.frame) * startPosDuration, 0);
    [self moveLeftHandle:leftTransationPoint];
    CGPoint rightTransationPoint = CGPointMake(CGRectGetWidth(self.collectionView.frame) * (-1 + startPosDuration + duration), 0);
    [self moveRightHandle:rightTransationPoint];
}


- (void)configDefault {
    self.controlSpace = 20;
    self.handleWidth = 10;
}

- (void)createViews {
    [self addSubview:self.collectionView];
    [self addSubview:self.leftCoverView];
    [self addSubview:self.rightCoverView];
    [self addSubview:self.leftImageView];
    [self addSubview:self.rightCoverImageView];
}

- (void)addEvent {
    UIPanGestureRecognizer *panGestureRecognizer = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(pan:)];
    [self.leftImageView addGestureRecognizer:panGestureRecognizer];

    UIPanGestureRecognizer *rightpanGestureRecongizer = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(rightPan:)];
    [self.rightCoverImageView addGestureRecognizer:rightpanGestureRecongizer];
}

- (void)pan:(UIPanGestureRecognizer *)panGestureRecognizer {
    CGPoint translatedPoint = [panGestureRecognizer translationInView:self];
    CGFloat x = panGestureRecognizer.view.center.x + translatedPoint.x;

    CGFloat limitWidth = self.limitMiniRate * CGRectGetWidth(self.collectionView.frame);

    if (x < self.controlSpace - CGRectGetWidth(self.leftImageView.frame) / 2.0f) {
        x = self.controlSpace - CGRectGetWidth(self.leftImageView.frame) / 2.0f;
    } else if (x + CGRectGetWidth(self.leftImageView.frame) / 2.0f + limitWidth >= CGRectGetMinX(self.rightCoverImageView.frame)) {
        if ([self canRightHandleMove2Right]) {
            [self moveRightHandle:translatedPoint];
        } else {
            x = CGRectGetMinX(self.rightCoverImageView.frame) - CGRectGetWidth(self.leftImageView.frame) / 2.0f - limitWidth;
        }
    }

    if ([self needRightHandleMove2Left:translatedPoint]) {
        [self moveRightHandle:translatedPoint];
    }

    CGFloat rate = (x - self.controlSpace + CGRectGetWidth(self.leftImageView.frame) / 2.0f) / CGRectGetWidth(self.collectionView.frame);
    switch (panGestureRecognizer.state) {
        case UIGestureRecognizerStateBegan:
        case UIGestureRecognizerStateChanged: {
            if (self.leftVideoCropBottomTrimChangeBlock) {
                self.leftVideoCropBottomTrimChangeBlock(rate);
            }
        }
            break;
        case UIGestureRecognizerStateEnded: {
            if (self.leftVideoCropBottomTrimEndBlock) {
                self.leftVideoCropBottomTrimEndBlock(rate);
            }
        }
            break;
    }

    if (self.trimInBlock) {
        self.trimInBlock(rate);
    }

    CGRect coverFrame = self.leftCoverView.frame;
    coverFrame.size.width = x - self.controlSpace + self.handleWidth / 2.0f;
    self.leftCoverView.frame = coverFrame;

    self.leftImageView.center = CGPointMake(x, CGRectGetHeight(self.frame) / 2.0f);
    [panGestureRecognizer setTranslation:CGPointZero inView:self];
}

- (void)rightPan:(UIPanGestureRecognizer *)panGestureRecognizer {

    CGPoint translatedPoint = [panGestureRecognizer translationInView:self];
    CGFloat x = panGestureRecognizer.view.center.x + translatedPoint.x;

    CGFloat limitWidth = self.limitMiniRate * CGRectGetWidth(self.collectionView.frame);

    if (x - CGRectGetWidth(self.rightCoverImageView.frame) / 2.0f - limitWidth < CGRectGetMaxX(self.leftImageView.frame)) {
        if ([self canLeftHandleMove2Left]) {
            [self moveLeftHandle:translatedPoint];
        } else {
            x = CGRectGetMaxX(self.leftImageView.frame) + CGRectGetWidth(self.rightCoverImageView.frame) / 2.0f + limitWidth;
        }
    } else if (x - CGRectGetWidth(self.rightCoverImageView.frame) / 2.0f >= CGRectGetWidth(self.frame) - self.controlSpace) {
        x = CGRectGetWidth(self.frame) - self.controlSpace;

    }


    if ([self needLeftHandleMove2Right:translatedPoint]) {
        [self moveLeftHandle:translatedPoint];
    }


    NSLog(@"collectionView/width = %lf x======%lf", CGRectGetWidth(self.collectionView.frame), x);

    CGFloat rate = (x - self.controlSpace - CGRectGetWidth(self.leftImageView.frame) / 2.0f) / CGRectGetWidth(self.collectionView.frame);
    switch (panGestureRecognizer.state) {
        case UIGestureRecognizerStateBegan:
        case UIGestureRecognizerStateChanged: {
            if (self.rightVideoCropBottomTrimChangeBlock) {
                self.rightVideoCropBottomTrimChangeBlock(rate);
            }
        }
            break;
        case UIGestureRecognizerStateEnded: {
            if (self.rightVideoCropBottomTrimEndBlock) {
                self.rightVideoCropBottomTrimEndBlock(rate);
            }
        }
            break;
    }

    if (self.trimOutBlock) {
        self.trimOutBlock(rate);
    }

    CGRect coverFrame = self.rightCoverView.frame;
    coverFrame.size.width = CGRectGetWidth(self.frame) - x - self.controlSpace;
    coverFrame.origin.x = x;
    self.rightCoverView.frame = coverFrame;

    self.rightCoverImageView.center = CGPointMake(x, CGRectGetHeight(self.frame) / 2.0f);

    [panGestureRecognizer setTranslation:CGPointZero inView:self];
}

- (BOOL)canRightHandleMove2Right {
    CGFloat rightHandleMinx = CGRectGetMinX(self.rightCoverImageView.frame);
    if (rightHandleMinx < CGRectGetWidth(self.frame) - self.controlSpace) {
        return YES;
    } else {
        return NO;
    }
}

- (BOOL)canLeftHandleMove2Left {
    CGFloat leftHandleMaxX = CGRectGetMaxX(self.leftImageView.frame);
    if (leftHandleMaxX <= self.controlSpace) {
        return NO;
    } else {
        return YES;
    }
}

- (BOOL)needRightHandleMove2Left:(CGPoint)transationPoint {
    CGFloat maxX = CGRectGetMinX(self.rightCoverImageView.frame);
    CGFloat minX = CGRectGetMaxX(self.leftImageView.frame) + transationPoint.x;

    CGFloat distance = maxX - minX;

    CGFloat limitMaxWidth = CGRectGetWidth(self.collectionView.frame) * self.limitMaxRate;
    if (distance >= limitMaxWidth) {
        return YES;
    } else {
        return NO;
    }
}


- (BOOL)needLeftHandleMove2Right:(CGPoint)transationPoint {
    CGFloat maxX = CGRectGetMinX(self.rightCoverImageView.frame) + transationPoint.x;
    CGFloat minX = CGRectGetMaxX(self.leftImageView.frame);

    CGFloat distance = maxX - minX;

    CGFloat limitMaxWidth = CGRectGetWidth(self.collectionView.frame) * self.limitMaxRate;
    if (distance >= limitMaxWidth) {
        return YES;
    } else {
        return NO;
    }
}

- (void)moveRightHandle:(CGPoint)transationPoint {
    CGFloat x = self.rightCoverImageView.center.x + transationPoint.x;

    if (x - CGRectGetWidth(self.rightCoverImageView.frame) / 2.0f >= CGRectGetWidth(self.frame) - self.controlSpace) {
        x = CGRectGetWidth(self.frame) - self.controlSpace + CGRectGetWidth(self.rightCoverImageView.frame) / 2.0f;
    }

    CGFloat rate = (x - self.controlSpace - CGRectGetWidth(self.rightCoverImageView.frame) / 2.0f) / CGRectGetWidth(self.collectionView.frame);
    if (self.trimOutBlock) {
        self.trimOutBlock(rate);
    }

    self.rightCoverImageView.center = CGPointMake(x, CGRectGetHeight(self.rightCoverImageView.frame) / 2.0f);

    CGRect coverFrame = self.rightCoverView.frame;
    coverFrame.size.width = CGRectGetWidth(self.frame) - x - self.controlSpace;
    coverFrame.origin.x = x;
    self.rightCoverView.frame = coverFrame;
}

- (void)moveLeftHandle:(CGPoint)transationPoint {
    CGFloat x = self.leftImageView.center.x + transationPoint.x;
    if (x < self.controlSpace - CGRectGetWidth(self.leftImageView.frame) / 2.0f) {
        x = self.controlSpace - CGRectGetWidth(self.leftImageView.frame) / 2.0f;
    }

    CGFloat rate = (x - self.controlSpace + CGRectGetWidth(self.leftImageView.frame) / 2.0f) / CGRectGetWidth(self.collectionView.frame);
    if (self.trimInBlock) {
        self.trimInBlock(rate);
    }

    self.leftImageView.center = CGPointMake(x, CGRectGetHeight(self.leftImageView.frame) / 2.0f);

    CGRect coverFrame = self.leftCoverView.frame;
    coverFrame.size.width = x - self.controlSpace + self.handleWidth / 2.0f;
    self.leftCoverView.frame = coverFrame;


}

#pragma mark - CollectionView

- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return self.images.count;
}

- (__kindof UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    ThumbCollectionCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:[ThumbCollectionCell identifier] forIndexPath:indexPath];
    UIImage *image = [[UIImage alloc] initWithContentsOfFile:self.images[indexPath.row]];
    [cell loadData:image];
    return cell;
}

#pragma mark - getter

- (UICollectionView *)collectionView {
    if (!_collectionView) {
        UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc] init];
        layout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
        layout.minimumInteritemSpacing = 0;
        layout.minimumLineSpacing = 0;
        layout.itemSize = CGSizeMake((CGRectGetWidth(self.frame) - 2 * self.controlSpace) / 6, CGRectGetHeight(self.frame));
        _collectionView = [[UICollectionView alloc] initWithFrame:CGRectMake(self.controlSpace, 0, CGRectGetWidth(self.frame) - 2 * self.controlSpace, CGRectGetHeight(self.frame)) collectionViewLayout:layout];
        _collectionView.backgroundColor = [UIColor whiteColor];
        _collectionView.dataSource = self;
        _collectionView.delegate = self;
        [_collectionView registerClass:[ThumbCollectionCell class] forCellWithReuseIdentifier:[ThumbCollectionCell identifier]];
        _collectionView.showsVerticalScrollIndicator = NO;
        _collectionView.showsHorizontalScrollIndicator = NO;
        _collectionView.bounces = NO;
    }
    return _collectionView;
}

- (ThumbImageView *)leftImageView {
    if (!_leftImageView) {
        _leftImageView = [[ThumbImageView alloc] initWithFrame:CGRectMake(self.controlSpace - self.handleWidth, 0, self.handleWidth, CGRectGetHeight(self.frame))];
        _leftImageView.thumbImage = [UIImage imageNamed:@"mv_left_handle"];
        _leftImageView.color = [UIColor clearColor];
    }
    return _leftImageView;
}

- (ThumbImageView *)rightCoverImageView {
    if (!_rightCoverImageView) {
        _rightCoverImageView = [[ThumbImageView alloc] initWithFrame:CGRectMake(CGRectGetWidth(self.frame) - self.controlSpace, 0, self.handleWidth, CGRectGetHeight(self.frame))];
        _rightCoverImageView.isRight = YES;
        _rightCoverImageView.thumbImage = [UIImage imageNamed:@"mv_right_handle"];
        _rightCoverImageView.color = [UIColor clearColor];
    }
    return _rightCoverImageView;
}

- (UIView *)leftCoverView {
    if (!_leftCoverView) {
        _leftCoverView = [[UIView alloc] initWithFrame:CGRectMake(self.controlSpace, 0, 0, CGRectGetHeight(self.frame))];
        _leftCoverView.backgroundColor = [UIColor blackColor];
        _leftCoverView.alpha = .5;
        _leftCoverView.userInteractionEnabled = NO;
    }
    return _leftCoverView;
}

- (UIView *)rightCoverView {
    if (!_rightCoverView) {
        _rightCoverView = [[UIView alloc] initWithFrame:CGRectMake(CGRectGetWidth(self.frame) - self.controlSpace, 0, 0, CGRectGetHeight(self.frame))];
        _rightCoverView.backgroundColor = [UIColor blackColor];
        _rightCoverView.alpha = .5;
        _rightCoverView.userInteractionEnabled = NO;
    }
    return _rightCoverView;
}

@end

```

# Example
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    [self.view addSubview:self.videoCropBottomTrimView];

    CGFloat duration = 100.0f; //s;
    CGFloat min = 2.0f;
    CGFloat max = 30.0f;
    self.videoCropBottomTrimView.limitMiniRate = min / duration;
    self.videoCropBottomTrimView.limitMaxRate = max / duration;

    [self.videoCropBottomTrimView loadDefaultTrim:0.3 duration:self.videoCropBottomTrimView.limitMaxRate];

    [self addCropEnent];
}

- (void)addCropEnent {
    __weak typeof(self) weakSelf = self;
    self.videoCropBottomTrimView.leftVideoCropBottomTrimChangeBlock = ^(CGFloat rate) {
        NSLog(@"leftVideoCropBottomTrimChangeBlock : rate = %lf", rate);
    };

    self.videoCropBottomTrimView.rightVideoCropBottomTrimChangeBlock = ^(CGFloat rate) {
        NSLog(@"rightVideoCropBottomTrimChangeBlock : rate = %lf", rate);
    };

    self.videoCropBottomTrimView.leftVideoCropBottomTrimEndBlock = ^(CGFloat rate) {
        NSLog(@"leftVideoCropBottomTrimEndBlock : rate = %lf", rate);
    };

    self.videoCropBottomTrimView.rightVideoCropBottomTrimEndBlock = ^(CGFloat rate) {
        NSLog(@"rightVideoCropBottomTrimEndBlock : rate = %lf", rate);
    };

    self.videoCropBottomTrimView.trimInBlock = ^(CGFloat rate) {
        NSLog(@"trimInBlock : rate = %lf", rate);
    };

    self.videoCropBottomTrimView.trimOutBlock = ^(CGFloat rate) {
        NSLog(@"trimOutBlock : rate = %lf", rate);
    };
}

- (BabyVideoCropBottomTrimView *)videoCropBottomTrimView {
    if (!_videoCropBottomTrimView) {
        _videoCropBottomTrimView = [[BabyVideoCropBottomTrimView alloc] initWithFrame:CGRectMake(0, 300, CGRectGetWidth(self.view.frame), 60)];
    }
    return _videoCropBottomTrimView;
}

```

# End
最近的毛衣战，经济行情走弱，每每想到前景，陷入无限的迷茫中..., 焦虑这东西没多大用，只是更多扰乱当下的判断，我要做的抬头看路，低头走路而已。


