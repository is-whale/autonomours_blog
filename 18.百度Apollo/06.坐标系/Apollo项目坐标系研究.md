- [Apollo项目坐标系研究_知行合一2018的博客-CSDN博客_apollo 坐标系](https://blog.csdn.net/davidhopper/article/details/79162385)

[百度Apollo项目](https://github.com/apolloauto)用到了多种坐标系，其中帮助文档提及的坐标系包括：全球地理坐标系（The Global Geographic coordinate system ）、局部坐标系—东-北-天坐标（The Local [Frame](https://so.csdn.net/so/search?q=Frame&spm=1001.2101.3001.7020) – East-North-Up，ENU）、车身坐标系—右-前-天坐标（The Vehicle Frame —Right-Forward-Up，RFU）、车身坐标系—前-左-天坐标（The Vehicle Frame —Front-Left-Up，FLU）。还有一种Frenet坐标系（又称Frenet–Serret公式），Apollo项目文档未提及，但在Apollo项目的规划模块中得到广泛使用。本文首先简介Apollo项目提及的三种坐标系，之后重点介绍Frenet坐标系及其与车辆坐标系之间的转换公式，最后对Apollo项目中Frenet坐标系与车辆坐标系的转换代码进行详细解释。

## 一、[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)文档提及的坐标系

### （一）全球地理坐标系

Apollo项目使用全球地理坐标系表示高精地图（ the high-definition map，HD Map）中诸元素的几何位置，通常包括：纬度（latitude）、经度（longitude）和海拔（elevation）。全球地理坐标系普遍采用地理信息系统（Geographic Information System，GIS）中用到的WGS-84坐标系（the World Geodetic System dating from 1984），如下图所示，注意海拔定义为椭球体高程（the ellipsoidal height）：
![1](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTI4MTQ1NzUwMDcw)

### （二）局部坐标系—东-北-天坐标（ENU）

局部坐标系定义为：

- X轴：指向东边
- Y轴：指向北边
- Z轴：指向天顶
  如下图所示：
  ![2](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTI4MTUxMTA3NDgx)
  ENU局部坐标系采用三维直角坐标系来描述地球表面，实际应用较为困难，因此一般使用简化后的二维投影坐标系来描述。在众多二维投影坐标系中，统一横轴墨卡托（The Universal Transverse Mercator ，UTM）坐标系是一种应用较为广泛的一种。UTM 坐标系统使用基于网格的方法表示坐标，它将地球分为 60 个经度区，每个区包含6度的经度范围，每个区内的坐标均基于横轴墨卡托投影，如下图所示：
  ![3](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTI4MTUzMzE5NjE5)

### （三）车身坐标系—右-前-天坐标（RFU）

车身坐标系—右-前-天坐标（RFU）的定义如下：

- X轴：面向车辆前方，右手所指方向
- Y轴：车辆前进方向
- Z轴：与地面垂直，指向车顶方向
  注意：车辆参考点为后轴中心。
  该坐标系一般用于感知模块。

如下图所示：
![4](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTI4MTUzOTI3NDk2)

### （四）车身坐标系—前-左-天坐标（FLU）

车身坐标系—前-左-天坐标（FLU）的定义如下：

- X轴：车辆前进方向
- Y轴：面向车辆前方，左手所指方向
- Z轴：与地面垂直，指向车顶方向
  注意：车辆参考点为后轴中心。
  该坐标系常用于实时相对地图模块。将RFU坐标系向左旋转90度即可得到FLU坐标系。

## 二、Frenet坐标系

Frenet坐标系又称Frenet–Serret公式，Apollo项目文档未提及，但在规划模块中广泛使用。Frenet–Serret公式用于描述粒子在三维欧氏空间 R 3 ℝ^3R3内沿一条连续可微曲线的运动学特征，如下图所示：
![5](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTI4MTU1MzU4Mzg5)

## 三、Frenet坐标系与笛卡尔坐标系的转换公式

为什么要将笛卡尔坐标系转换为Frenet坐标系？因为可以这样可以将车辆的二维运动问题解耦合为两个一维运动问题。显然，一维问题比二维问题容易求解，这就是笛卡尔坐标系转换为Frenet坐标系的必要性。
![6](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTgwMTI5MTQ1OTQ4MzE1)

## 四、Apollo项目中Frenet坐标系与笛卡尔坐标系转换代码

函数声明文件planning/math/frame_conversion/cartesian_frenet_conversion.h：

```cpp
#ifndef MODULES_PLANNING_MATH_FRAME_CONVERSION_CARTESIAN_FRENET_CONVERSION_H_
#define MODULES_PLANNING_MATH_FRAME_CONVERSION_CARTESIAN_FRENET_CONVERSION_H_

#include <array>

#include "modules/common/math/vec2d.h"

namespace apollo {
namespace planning {

// Notations:
// s_condition = [s, s_dot, s_ddot]
// s: longitudinal coordinate w.r.t reference line.
// s_dot: ds / dt
// s_ddot: d(s_dot) / dt
// d_condition = [d, d_prime, d_pprime]
// d: lateral coordinate w.r.t. reference line
// d_prime: dd / ds
// d_pprime: d(d_prime) / ds
// l: the same as d.
class CartesianFrenetConverter {
 public:
  CartesianFrenetConverter() = delete;
  /**
   * Convert a vehicle state in Cartesian frame to Frenet frame.
   * Decouple a 2d movement to two independent 1d movement w.r.t. reference
   * line.
   * The lateral movement is a function of longitudinal accumulated distance s
   * to achieve better satisfaction of nonholonomic constraints.
   */
  static void cartesian_to_frenet(const double rs, const double rx,
                                  const double ry, const double rtheta,
                                  const double rkappa, const double rdkappa,
                                  const double x, const double y,
                                  const double v, const double a,
                                  const double theta, const double kappa,
                                  std::array<double, 3>* const ptr_s_condition,
                                  std::array<double, 3>* const ptr_d_condition);
  /**
   * Convert a vehicle state in Frenet frame to Cartesian frame.
   * Combine two independent 1d movement w.r.t. reference line to a 2d movement.
   */
  static void frenet_to_cartesian(const double rs, const double rx,
                                  const double ry, const double rtheta,
                                  const double rkappa, const double rdkappa,
                                  const std::array<double, 3>& s_condition,
                                  const std::array<double, 3>& d_condition,
                                  double* const ptr_x, double* const ptr_y,
                                  double* const ptr_theta,
                                  double* const ptr_kappa, double* const ptr_v,
                                  double* const ptr_a);

  // given sl point extract x, y, theta, kappa
  static double CalculateTheta(const double rtheta, const double rkappa,
                               const double l, const double dl);

  static double CalculateKappa(const double rkappa, const double rdkappa,
                               const double l, const double dl,
                               const double ddl);

  static common::math::Vec2d CalculateCartesianPoint(
      const double rtheta, const common::math::Vec2d& rpoint, const double l);
  /**
   * @brief: given sl, theta, and road's theta, kappa, extract derivative l,
   *second order derivative l:
   */
  static double CalculateLateralDerivative(const double theta_ref,
                                           const double theta, const double l,
                                           const double kappa_ref);

  // given sl, theta, and road's theta, kappa, extract second order derivative
  static double CalculateSecondOrderLateralDerivative(
      const double theta_ref, const double theta, const double kappa_ref,
      const double kappa, const double dkappa_ref, const double l);
};

}  // namespace planning
}  // namespace apollo
```

函数实现文件planning/math/frame_conversion/cartesian_frenet_conversion.cc：

```cpp
#include "modules/planning/math/frame_conversion/cartesian_frenet_conversion.h"

#include <cmath>

#include "modules/common/log.h"
#include "modules/common/math/math_utils.h"

namespace apollo {
namespace planning {

using apollo::common::math::Vec2d;

void CartesianFrenetConverter::cartesian_to_frenet(
    const double rs, const double rx, const double ry, const double rtheta,
    const double rkappa, const double rdkappa, const double x, const double y,
    const double v, const double a, const double theta, const double kappa,
    std::array<double, 3>* const ptr_s_condition,
    std::array<double, 3>* const ptr_d_condition) {
  const double dx = x - rx;
  const double dy = y - ry;

  const double cos_theta_r = std::cos(rtheta);
  const double sin_theta_r = std::sin(rtheta);

  const double cross_rd_nd = cos_theta_r * dy - sin_theta_r * dx;
  // 求解d
  ptr_d_condition->at(0) =
      std::copysign(std::sqrt(dx * dx + dy * dy), cross_rd_nd);

  const double delta_theta = theta - rtheta;
  const double tan_delta_theta = std::tan(delta_theta);
  const double cos_delta_theta = std::cos(delta_theta);

  const double one_minus_kappa_r_d = 1 - rkappa * ptr_d_condition->at(0);
  // 求解d' = dd / ds
  ptr_d_condition->at(1) = one_minus_kappa_r_d * tan_delta_theta;

  const double kappa_r_d_prime =
      rdkappa * ptr_d_condition->at(0) + rkappa * ptr_d_condition->at(1);

  // 求解d'' = dd' / ds
  ptr_d_condition->at(2) =
      -kappa_r_d_prime * tan_delta_theta +
      one_minus_kappa_r_d / cos_delta_theta / cos_delta_theta *
          (kappa * one_minus_kappa_r_d / cos_delta_theta - rkappa);

  // 求解s
  ptr_s_condition->at(0) = rs;
  // 求解ds / dt
  ptr_s_condition->at(1) = v * cos_delta_theta / one_minus_kappa_r_d;

  const double delta_theta_prime =
      one_minus_kappa_r_d / cos_delta_theta * kappa - rkappa;
  // 求解d(ds) / dt
  ptr_s_condition->at(2) =
      (a * cos_delta_theta -
       ptr_s_condition->at(1) * ptr_s_condition->at(1) *
           (ptr_d_condition->at(1) * delta_theta_prime - kappa_r_d_prime)) 
           / one_minus_kappa_r_d;
  return;
}

void CartesianFrenetConverter::frenet_to_cartesian(
    const double rs, const double rx, const double ry, const double rtheta,
    const double rkappa, const double rdkappa,
    const std::array<double, 3>& s_condition,
    const std::array<double, 3>& d_condition, double* const ptr_x,
    double* const ptr_y, double* const ptr_theta, double* const ptr_kappa,
    double* const ptr_v, double* const ptr_a) {
  CHECK(std::abs(rs - s_condition[0]) < 1.0e-6)
      << "The reference point s and s_condition[0] don't match";

  const double cos_theta_r = std::cos(rtheta);
  const double sin_theta_r = std::sin(rtheta);

  *ptr_x = rx - sin_theta_r * d_condition[0];
  *ptr_y = ry + cos_theta_r * d_condition[0];

  const double one_minus_kappa_r_d = 1 - rkappa * d_condition[0];

  const double tan_delta_theta = d_condition[1] / one_minus_kappa_r_d;
  const double delta_theta = std::atan2(d_condition[1], one_minus_kappa_r_d);
  const double cos_delta_theta = std::cos(delta_theta);

  *ptr_theta = common::math::NormalizeAngle(delta_theta + rtheta);

  const double kappa_r_d_prime =
      rdkappa * d_condition[0] + rkappa * d_condition[1];
  *ptr_kappa = (((d_condition[2] + kappa_r_d_prime * tan_delta_theta) *
                 cos_delta_theta * cos_delta_theta) /
                    (one_minus_kappa_r_d) +
                rkappa) *
               cos_delta_theta / (one_minus_kappa_r_d);

  const double d_dot = d_condition[1] * s_condition[1];
  *ptr_v = std::sqrt(one_minus_kappa_r_d * one_minus_kappa_r_d *
                         s_condition[1] * s_condition[1] +
                     d_dot * d_dot);

  const double delta_theta_prime =
      one_minus_kappa_r_d / cos_delta_theta * (*ptr_kappa) - rkappa;

  *ptr_a = s_condition[2] * one_minus_kappa_r_d / cos_delta_theta +
           s_condition[1] * s_condition[1] / cos_delta_theta *
               (d_condition[1] * delta_theta_prime - kappa_r_d_prime);
}

double CartesianFrenetConverter::CalculateTheta(const double rtheta,
                                                const double rkappa,
                                                const double l,
                                                const double dl) {
  return common::math::NormalizeAngle(rtheta + std::atan2(dl, 1 - l * rkappa));
}

double CartesianFrenetConverter::CalculateKappa(const double rkappa,
                                                const double rdkappa,
                                                const double l, const double dl,
                                                const double ddl) {
  double denominator = (dl * dl + (1 - l * rkappa) * (1 - l * rkappa));
  if (std::fabs(denominator) < 1e-8) {
    return 0.0;
  }
  denominator = std::pow(denominator, 1.5);
  const double numerator = rkappa + ddl - 2 * l * rkappa * rkappa -
                           l * ddl * rkappa + l * l * rkappa * rkappa * rkappa +
                           l * dl * rdkappa + 2 * dl * dl * rkappa;
  return numerator / denominator;
}

Vec2d CartesianFrenetConverter::CalculateCartesianPoint(const double rtheta,
                                                        const Vec2d& rpoint,
                                                        const double l) {
  const double x = rpoint.x() - l * std::sin(rtheta);
  const double y = rpoint.y() + l * std::cos(rtheta);
  return Vec2d(x, y);
}

double CartesianFrenetConverter::CalculateLateralDerivative(
    const double rtheta, const double theta, const double l,
    const double rkappa) {
  return (1 - rkappa * l) * std::tan(theta - rtheta);
}

double CartesianFrenetConverter::CalculateSecondOrderLateralDerivative(
    const double rtheta, const double theta, const double rkappa,
    const double kappa, const double rdkappa, const double l) {
  const double dl = CalculateLateralDerivative(rtheta, theta, l, rkappa);
  const double theta_diff = theta - rtheta;
  const double cos_theta_diff = std::cos(theta_diff);
  const double res = -(rdkappa * l + rkappa * dl) * std::tan(theta - rtheta) +
                     (1 - rkappa * l) / (cos_theta_diff * cos_theta_diff) *
                         (kappa * (1 - rkappa * l) / cos_theta_diff - rkappa);
  if (std::isinf(res)) {
    AWARN << "result is inf when calculate second order lateral "
             "derivative. input values are rtheta:"
          << rtheta << " theta: " << theta << ", rkappa: " << rkappa
          << ", kappa: " << kappa << ", rdkappa: " << rdkappa << ", l: " << l
          << std::endl;
  }
  return res;
}
```

参考资料：

1. https://github.com/ApolloAuto/apollo/blob/master/docs/specs/coordination.pdf
2. https://en.wikipedia.org/wiki/Frenet–Serret_formulas
3. Optimal trajectory generation for dynamic street scenarios in a Frenét Frame, http://ieeexplore.ieee.org/document/5509799/citations?tabFilter=papers