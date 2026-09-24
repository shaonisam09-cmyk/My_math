# A model predictive control for a multi-chiller system in data center considering whole system energy conservation

Jing Zhao <sup>\*</sup>, Ziyi Chen , Haonan Li , Dehan Liu

Tianjin Key Laboratory of Built Environment and Energy Application, School of Environmental Science and Engineering, Tianjin University, Tianjin 300072, China

A R T I C L E I N F O

Keywords: Data center energy-saving Long and short-term memory Multi-chiller intelligent control Model predictive control

## A B S T R A C T

To meet the thermal environment requirements for the continuous and stable operation of information technology (IT) equipment in data rooms, the cooling system needs to operate all day, which has become the second largest energy-consuming system in data centers. The absence of an effective operational control strategy in the data center cooling process results in high energy consumption and poor power usage effectiveness (PUE) of the data center. This study proposes a model predictive control (MPC) strategy considering the constraint of whole system energy conservation for a multi-chiller system in a data center. This strategy aims to maintain the data center server room temperature stable in the IT equipment working environment. and at the same time. the energy consumption of the cooling system is significantly reduced, thus the PUE of the data center is lowered. The long and short-term memory (LSTM) neural network prediction models for data center cooling load and server room temperature were constructed using the mechanism-coupled data law method. The MPC algorithmic structure coupled with cooling load prediction model and server room temperature prediction model. It takes the balance between supply and demand of cooling capacity as a constraint. The cost function of multi-chiller system control considers the temperature-controlling error in the server room and the power consumption of the chilledwater system. A particle swarm optimization (PSO) algorithm is used to solve the optimal configuration strategy of the chilled water flow rate and the water supply temperature of each chiller, which realizes the dynamic control of the chilled-water system. A dynamic regulation model was developed for a data center in North China. TRNSYS was utilized for verification based on the actual measured data and relevant meteorological parameters of the case building. Compared with the operation results of the data center cooling system with proportional integral derivative (PID) control and fuzzy control. The results show that under MPC strategy, the stability of the server room temperature has improved by 27.77% compared to PID control and by 18.08% compared to fuzzy control, demonstrating the superiority of MPC in temperature stability control in the server room. In terms of chilled-water system energy consumption, compared to the PID control and fuzzy control strategies, the MPC strategy has achieved energy savings of 11.81% and 7.58%, respectively, showing significant energy-saving effects.

## 1. Introduction

With the development of new-generation information technology, the scale of data centers is growing rapidly, and their power consumption is also climbing year by year [1]. The data shows that by the end of 2022, China had a total of 6.7 million data center racks in use [2], with a power consumption of 270 billion kW⋅h. It is projected that by 2030, the power consumption of data centers may double compared to the 2022 level. Currently, the average power usage effectiveness (PUE) of large data centers in China is 1.55 [3]. However, the PUE of new large and ultra-large data centers needs to be less than 1.3 by 2025 [4]. Data center information technology (IT) equipment is the largest energyconsuming system [5]. However, the electrical loads of IT equipment are inflexible and cannot be shifted or regulated. Therefore, to optimize energy efficiency in data centers, it is crucial to harness the energysaving potential of flexible load systems, such as the cooling system. The cooling system usually runs all year round to maintain the thermal and humid environment required by IT equipment, and it accounts for about 40% of the total energy consumption of the data center [6–8]. In data centers, IT equipment serves as the primary heat source.

Consequently, accurately predicting cooling demands and achieving precise temperature control in server rooms to minimize energy con sumption poses a new challenge in scenarios where heat sources are distinctly identified.

In the operation control technology of data center cooling systems, the control objectives should simultaneously consider reducing system operation energy consumption and stabilizing server room parameters. Currently, related research mainly focuses on applying advanced control methods, such as predictive techniques and artificial intelligence, to optimize cooling systems. Durand-Estebe et al. [9] proposed an approach to optimize data centers using proportional integral derivative (PID) control in computational fluid dynamics (CFD) simulations to reduce energy consumption. Chen et al. [10] used a neural network (NN) model to construct the load factor and energy consumption model of chiller units and optimized loads of multiple chillers by particle swarm optimization (PSO) algorithm to achieve the minimum energy con sumption of the system. Yao et al. [11] optimized the energy consumption of the air conditioning system and the stability of rack inlet temperature by using the non-dominated sorting genetic algorithm II (NSGA-II) to solve the multi-objective optimization problem in data centers. Zhang et al. [12] established a prediction model for the collaboration of IT equipment and cooling equipment and carried out optimization and control for the multi-objectives such as IT equipment load distribution, server temperature, and PUE. However, rule-based control relies on expert experience and cannot achieve the global optimal solution, with limited energy-saving potential [13]. The opti mization effect of data-driven control depends on the quality and quantity of historical data. If the historical data is insufficient to encompass all potential operating conditions, the optimized operational strategy represents merely the best choice within the historical context [14]. In conclusion, considering the problems of difficult regulation and high energy consumption of cooling systems in multi-cooling source data centers, it is crucial to formulate a multi-objective control strategy for the synergistic configuration of multi-chiller cooling supply and optimize the operating parameters of the cooling system.

In terms of control modeling, model predictive control (MPC) is based on a dynamic model of the system [15–20]. It can accurately predict the future state of the system, achieve precise control of the system through rolling optimization to solve for the optimal control parameters, and deliver significant energy-saving effects [21–23]. In addition, the MPC strategy has been applied in energy saving and consumption reduction in data centers and has achieved satisfactory control results. Zhu et al. [24] proposed an advanced MPC approach to regulate the operating parameters of chillers and cold storage units to maximize the energy efficiency of the cooling system. Wang et al. [25] proposed a hierarchical dispatch strategy of a hybrid energy storage system in an internet data center with MPC to improve the stability and economy of the power system and to increase the utilization of uninterrupted power system (UPS). Ogawa et al. [26] proposed a MPC method for IT equipment and cooling equipment that optimizes the central processing unit (CPU) frequency, server fans, and temperature change rate of the server room to achieve over 30% energy savings. However, with the growth of the data center scale, the cooling sources of the cooling system are gradually diversified. There is still a research gap in achieving precise dynamic intelligent control by applying MPC to make the temperature control of server rooms follow the temporal variations of internal and external disturbance variables. Therefore, establishing a MPC algorithm framework coupled with cooling load and server room temperature prediction models based on the constraint of whole system energy conservation for a multi-chiller system can fill this gap.

Accurately predicting the state of the control target is a necessary part in MPC. To achieve predictive control of the data center cooling system, establishing an effective prediction model for both the cooling system and the server room state is imperative. Park et al. [27] used an artificial neural network (ANN) prediction model to predict the computer room air handler supply air temperature of a central chilled water system. Zapater et al. [28] proposed a CPU temperature prediction method involving server power, fan speed, inlet temperature, and past CPU temperatures, which modeled this prediction method by a syntactic evolutionary algorithm. Tarutani et al. [29] constructed a prediction model for cabinet-level temperature distribution in data centers based on the random forest (RF) method by selecting parameters such as server power consumption, server inlet temperature, and air conditioning air supply temperature. However, previous studies on temperature prediction in data centers have focused on the scale of servers and racks, and there has been relatively little research on temperature prediction for the entire data center.

Although the aforementioned optimization methods can reduce the energy consumption of the data center cooling systems to some extent, there are still some limitations:

(1) Most temperature control technologies based on PID or fuzzy control overlook the slow response of cooling supply and the high energy consumption caused by over-supply due to constraints on the overall dynamic demand for cooling brought by internal and external disturbance variables;

(2) There is a lack of research on accurate cooling load or indoor temperature prediction for the entire server room and considering dynamic IT equipment load.

To address these issues, this paper proposes a MPC strategy for a multi-chiller system in data center based on the constraint of whole system energy conservation. This strategy is of great significance for reducing energy consumption and achieving temperature control in data centers. The main contributions of this study are as follows:

(1) A MPC strategy for the cooperative allocation of cooling capacity from multiple chillers with different energy efficiency was proposed by taking the balance between supply and demand of cooling capacity and considering the precise control of the server room temperature. The optimal chilled water temperature and flow rate allocation strategy for multiple chillers was dynamically solved by a rolling optimization algorithm;

(2) Data center cooling load and indoor temperature time serie prediction models based on long and short-term memory (LSTM) were developed using a mechanism analysis coupled with a data-driven approach. The time series of IT equipment load was introduced as the input feature vector of the prediction models, which improved the robustness of the prediction models.

## 2. Methodology

The technical route used in this study is shown in Fig. 1. The prediction models employed in this study include the cooling load and server room temperature prediction models using LSTM neural networks. The MPC strategy for multi-chiller system in data center utilizes intelligent prediction algorithms and intelligent rolling optimization methods. This approach coordinates multiple chillers of varying capacities to achieve stable temperatures in the internet data center (IDC) room while optimizing energy efficiency of the cooling system.

## 2.1. MPC strategy

The details of the MPC strategy implementation are as follows: the MPC design mainly consists of a prediction model and an optimization algorithm [30,31]. This study optimized the MPC algorithm framework by coupling cooling load and server room temperature prediction models for multiple chillers. It takes the balance between supply and demand of cooling capacity as a constraint and considers the precise control of the server room temperature.

2.1.1. Optimization algorithm structure for multi-chiller based on whole system energy conservation constraint

The control algorithm structure of this study is shown in Fig. 2.

(1) Based on the cooling load prediction model, the cooling load

![](images/cf2da98623faa3c0894b043e002539e711f1dfd0b226804ad31d582fdff2cfcc.jpg)  
Fig. 1. Technical line of research.

$L _ { \mathrm { c o o l i n g } } ( t { + } 1 )$ at the moment t+1 is predicted. Since the parameters required by the cooling load prediction model are known, the predicted $L _ { \mathrm { c o o l i n g } } ( t { + } 1 )$ is a constant value and does not need to be optimized through the optimization algorithm.

(2) Based on the temperature prediction model. the server room temperature $T _ { \mathrm { i n } } ( t { + } 1 ) ^ { * }$ at the moment t+1 is predicted by inputting known relevant parameters (including the outdoor temperature $T _ { \mathrm { o u t } } ( t { + } 1 )$ at the moment t+1, which can be derived from the weather forecast), as well as the to-be-optimized water supply temperature $T _ { \mathrm { s u p p l y } } ( t { + } 1 ) ^ { * }$ at the moment t+1. Since the water supply temperature $T _ { \mathrm { s u p p l y } } ( t { + } 1 ) ^ { * }$ needs to be further optimized, this predicted temperature is not the final room temperature prediction.

(3) Based on the system-wide energy conservation constraint equation set, as shown in Eq. (1), the sum of the cooling capacity provided by each chiller satisfies the predicted cooling load demand. Assuming that the chilled water flow rate of the screw chiller and the magnetic levi tation chiller at the moment of t+1 is $V _ { \mathrm { s c r e w } } ( t { + } 1 ) ^ { * }$ and $V _ { \mathrm { m a g l e v } } ( t { + } 1 ) ^ { * }$ respectively, and thus calculating the return water temperature $T _ { \mathrm { r e t u r n } } ( t { + } 1 ) ^ { * }$ under the corresponding assumed values. All values are hypothetical and need to be substituted into the rolling optimization session to solve for the optimal value.

$$
\left\{ \begin{array}{c} L _ {\text { cooling }} (t + 1) = 4. 1 9 / 3. 6 \cdot V _ {\text { chw }} (t + 1) ^ {*} \cdot \Delta T _ {\text { chw }} (t + 1) ^ {*} \\ V _ {\text { chw }} (t + 1) ^ {*} = 2 \cdot [ V _ {\text { screw }} (t + 1) ^ {*} + V _ {\text { maglev }} (t + 1) ^ {*} ] \\ \Delta T _ {\text { chw }} (t + 1) ^ {*} = T _ {\text { return }} (t + 1) ^ {*} - T _ {\text { supply }} (t + 1) ^ {*} \end{array} \right.\tag{1}
$$

Where t+1 indicates the specific moment; $L _ { c o o l i n g }$ is the predicted value of cooling load, $\mathrm { k W } ; V _ { c h w } { ^ { * } } , V _ { s c r e w } { ^ { * } }$ and $V _ { m a g l e \nu } { ^ * }$ are the total chilled water flow rate, the chilled water flow rate borne by a single screw unit and a single magnetic levitation unit, respectively, m<sup>3</sup>/h; $\Delta T _ { c h w } \mathrm { ^ { * } , } T _ { s u p p l y } \mathrm { ^ { * } }$ and $T _ { r e t u r n } { ^ * }$ are the temperature difference between the supply and re turn water, the supply water temperature and the return water temperature, respectively, <sup>◦</sup>C.

(4) Based on the performance-energy consumption models of the aircooled chillers, the chilled water flow rate $V _ { s c r e w } ( t { + } 1 ) ^ { * }$ and $V _ { m a g l e \nu } ( t { + } 1 )$ \*, the return water temperature $T _ { r e t u r n } ( t + 1 ) ^ { * }$ , and the predicted cooling load $L _ { c o o l i n g } ( t { + } 1 )$ at the time of t+1 mentioned above are brought in respectively to solve the optimized power consumption values $P _ { s c r e w , 1 } ( t + 1 ) ^ { * } , P _ { s c r e w , 2 } ( t + 1 ) ^ { * } , P _ { m a g l e \nu , 1 } ( t + 1 ) ^ { * }$ and $P _ { m a g l e \nu , 2 } ( t { + } 1 ) ^ { * }$ of the two screw chillers and the two maglev chillers. Eq. (2) is an expression for the total chiller power consumption $P _ { c h i l l e r } ( t { + } 1 ) \ ^ { * }$

$$
\begin{array}{r l} P _ {\text { chiller }} (t + 1) ^ {*} & = P _ {\text { screw }, 1} (t + 1) ^ {*} + P _ {\text { screw }, 2} (t + 1) ^ {*} + P _ {\text { maglev }, 1} (t + 1) ^ {*} \\ & \quad + P _ {\text { maglev }, 2} (t + 1) ^ {*} \end{array}\tag{2}
$$

where t+1 denotes the specific moment; $P _ { s c r e w , 1 } { ^ { * } } , P _ { s c r e w , 2 } { ^ { * } } , P _ { m a g l e \nu , 1 } { ^ { * } } ,$ $P _ { m a g l e \nu , 2 } { ^ * } ,$ and $P _ { c h i l l e r } *$ denote the power consumption of each screw chiller, the power consumption of each magnetic levitation chiller, and the total power consumption of the four chillers, respectively, kW⋅h.

(5) Based on the performance-energy consumption model of the chilled water pump, bring in the chilled water flow rate $V _ { c h w } ( t + 1 ) ^ { * }$ at the above moment t+1, and solve for the power consumption value of the chilled water pump to be optimized, $P _ { p u m p } ( t { + } 1 ) ^ { * }$

(6) Select the difference between the predicted server room temperature $T _ { i n } ( t { + } 1 ) ^ { * }$ at the moment of t+1 and the set temperature $T _ { s e t } ( t { + } 1 )$ as the optimization objective one, select the chilled-water system energy consumption as the optimization objective two, construct the cost function as in Eq. (3), and select the optimizer for rolling optimization solving.

$$
\left\{ \begin{array}{c} J (t) = \alpha \cdot \left[ T _ {\text { in }} (t + 1) ^ {*} - T _ {\text { set }} (t + 1) \right] ^ {2} + \beta \cdot \left[ P _ {\text { chiller }} (t + 1) ^ {*} + 3 \cdot P _ {\text { pump }} (t + 1) ^ {*} \right] ^ {2} \\ \alpha + \beta = 1 \end{array} \right.\tag{3}
$$

where t and t+1 denote specific moments; $T _ { i n } { ^ * }$ is the temperature prediction value, and $T _ { s e t }$ is the temperature setting value, ${ ^ \circ } \mathbf { C } ; P _ { c h i l l e r } { ^ { \ast } }$ and $P _ { p u m p } { ^ { * } }$ denote the total power consumption of the chillers and the power consumption of a single pump, respectively, kW⋅h; α and β are dimensionless weight coefficients, and the temperature control of the server room is equally important as the energy saving of the cooling system in the cooling scenario of the data center, so the relative weights of the two should be close in the cost function. Considering that the absolute value of the temperature control term in the cost function is around 3.6×10<sup>-</sup> $^ 1 \circ _ { \mathsf { C } } 2$ and the absolute value of the system energy consumption control term is around $1 . 9 5 \times 1 0 ^ { 5 } ~ ( \mathrm { k W } { \cdot } \mathrm { h } ) ^ { 2 } , \alpha { = } 1 { - } 1 . 8 { \times } 1 0 ^ { - 6 }$ and $\beta { = } 1 . 8 { \times } 1 0 ^ { - 6 }$ are set to ensure that the two controlled targets are in the same order of magnitude after being weighted with the weight coefficients.

![](images/47f36eac74f0a2350710677cdcd8c0a3e96f45ec5f39bfad50bea480f1f0f432.jpg)  
Fig. 2. MPC algorithm structure for a muti-chiller system in the data center.

The four variables $V _ { s c r e w } ^ { * } , V _ { m a g l e \nu } ^ { * } , T _ { s u p p l y } ^ { } { } ^ { * } ,$ and $T _ { r e t u r n } { ^ { * } }$ have constraint ranges during the optimization solution process, and the upper and lower bounds of the constraint ranges of each parameter are determined based on the maximum and minimum values of the parameters collected from the experiment, as shown in Table 1.

## 2.1.2. MPC strategy design

Due to the IDC room temperature control strategy being constant temperature target control, in this MPC logic, there is no distinction between setpoint and reference trajectory values. According to the recommended temperature range for data rooms in the ASHRAE Stan dard [32], and the actual operational control requirements of the data center, both the setpoint and the reference trajectory values are set at 22<sup>◦</sup>C. Therefore, the proposed MPC strategy in this paper aims to maintain the server room temperature close to and stable at around 22<sup>◦</sup>C while minimizing energy consumption as much as possible. The controlled parameters include chilled water supply temperature $T _ { s u p p l y } ,$ magnetic levitation chiller chilled water flow $V _ { m a g l e \nu }$ , and screw chiller chilled water flow $V _ { s c r e w } .$ Considering the frequent changes in IT equipment load in the computer room, the prediction time domain is set to one prediction time step. Fig. 3 shows the implementation flowchart of the control strategy. The rolling optimization process follows the red path in the figure. When the rolling optimization reaches the convergence target, the optimized control parameter values are passed to the TRNSYS model for system control simulation. The simulated value of the server room temperature $T _ { i n , s }$ and actual cooling load value $Q _ { c o o l i n g , s }$ are compared with predicted values $T _ { i n , p }$ and $L _ { c o o l i n g }$ to make corrections. The simulation results are then used as input for the next prediction time step in the rolling optimization process, as shown by the blue path in Fig. 3.

Table 1  
Constraint ranges for each parameter to be optimized.

<table><tr><td></td><td> $V_{screw}^{*}(m^{3}/h)$ </td><td> $V_{maglev}^{*}(m^{3}/h)$ </td><td> $T_{supply}^{*}(°C)$ </td><td> $T_{return}^{*}(°C)$ </td></tr><tr><td>Upper bound</td><td>150</td><td>45</td><td>11.5</td><td>15.5</td></tr><tr><td>Lower bound</td><td>120</td><td>30</td><td>10.0</td><td>14.5</td></tr></table>

## 2.2. Prediction models

## 2.2.1. Selection of input parameters

The selection of input parameters for prediction models will directly affect the complexity of calculations and the accuracy of predictions, and should be chosen according to the data relationship of the prediction target. In this study, grey relation analysis (GRA) [33] was used to evaluate the degree of influence between different factors in order to select and get the key factors affecting the target parameters. Compared with the classical correlation analysis methods such as correlation analysis and similarity coefficient, the GRA method does not have specific requirements on the number of samples, which is suitable for the correlation analysis of small-sample data where the relationship between factors is not clear [34]. The gray correlation value GRY is a positive number not exceeding 1. The closer to 1, the stronger the correlation. Generally, when GRY>0.8, the data are considered to be strongly correlated with each other, and when GRY<0.6, the data are considered to be weakly correlated with each other [35,36].

![](images/026c4e617bc57a2788bd7194d9348ea71c20871613044507eab58202a07fa208.jpg)  
Fig. 3. MPC logic chart.

The building structure and load characteristics of the data center have the following characteristics: (1) IDC room area has no external windows, good airtightness, and good thermal insulation performance of the external enclosure; (2) Data center cooling load is dominated by sensible heat load and mainly comes from IT equipment heating.

For any given moment, the IDC room cooling load is a relatively determinate value. According to the breakdown of factors affecting the cooling load, it can be divided into three main categories: external factors, internal factors, and historical factors. In terms of external fac tors, the main consideration is the effect of the outdoor dry bulb tem perature on the cooling load through the envelope. In terms of internal factors, the main consideration is the impact of IT equipment heating. Since the IT equipment heating rate is relatively fixed, the IT equipment load can be used to replace the IT equipment heating rate when selecting input parameters. In terms of historical factors, considering the thermal inertia of the envelope, the state parameters of the internal and external environments at a number of moments in the past will also have an impact on the state parameters of the building at the current moment. Therefore, in order to further improve the accuracy of the cooling load prediction for IDC rooms, the server room temperature, outdoor tem perature, IT equipment load, and cooling supply over the past two-time steps are included in the input parameter sequence, while the energy accumulation effect is considered to expand the above parameters on the time axis. In this study, the time step is 20 min. Therefore, considering the moment t+1 as the predicted time, we expand forward by two-time steps to select the filtered parameters. The specific nine parameters are shown in Fig. 4, of which the outdoor temperature $T _ { o u t } ( t + 1 )$ at the moment of t+1 can be obtained through the meteorological forecast, and thus is also analyzed as the input parameter to be selected.

The factors influencing server room temperature vary dynamically. Therefore, additional factors from the cooling system are included based on potential influencing factors in the data center cooling load. Among them, the influence of external factors, internal factors, and historical factors on the server room temperature is similar to the influence on the cooling load. Therefore, the outdoor temperature, IT equipment load, server room temperature, and cooling supply are still considered as input parameters for the server room temperature prediction model. In terms of cold source factors, the supply water temperature of chilled water serves as an automatically collected parameter with good data continuity and convenient acquisition, making it suitable as a candidate input parameter category for the model. Similarly to the logic applied in the time series of cooling load prediction parameters, a total of 11 initial parameters are considered for data center temperature prediction. The specific parameters are shown in Fig. 5.

## 2.2.2. Cooling load and temperature prediction models based on LSTM

With the development of deep learning, LSTM neural networks have shown better adaptability and robustness in short-term prediction due to their advantages in modeling time series [37], and the use of LSTM has better accuracy in data center cooling load and temperature prediction [38]. Therefore, this study adopts an LSTM neural network to predict the cooling load and server room temperature of the data center. LSTM neural network is mainly composed of four parts, including memory cell, forget gate, input gate, and output gate, and its basic structure is shown in Fig. 6.

The main process of constructing the prediction model is shown in Fig. 7, and a detailed introduction is as follows:

(1) Data preprocessing and normalization

After the basic collection of data, preprocessing is needed to improve data quality. For individual outliers or missing data, linear interpolation is used to correct outliers and supplement missing data. For datasets with multiple consecutive outliers or missing data, which may result from systematic errors in experimental procedures during testing, these data will be discarded. Additionally, due to the different dimensions of the collected data, which are not on the same scale, for example, the temperature of the server room is in the order of $2 \times 1 0 ^ { 1 ~ \circ } \mathrm { C } ,$ , while the cooling load is concentrated in the order of $1 \times 1 0 ^ { 3 } \mathrm { k W } ,$ so it is necessary to normalize and dimensionless all data. This is to facilitate the neural network model in weighting different feature vectors. The calculation and processing method is shown in Eq. (4).

![](images/7614a38ec8359b9d5b81fe2b0e3b523d1ccb1da57c3f04717c3d46f51304194b.jpg)  
Fig. 4. Parameters to be selected for the cooling load prediction model.

![](images/52e5fad79fb45ad3378c4b87de600a4829354bf30de52f3de9c349f4b4ef06c7.jpg)  
Fig. 5. Parameters to be selected for the server room temperature prediction model.

![](images/d30d39b635920cda1181f361f820423b132e38e65e6ca80a0b78e2af24397128.jpg)  
Fig. 6. Structure of LSTM neural network.

![](images/0d881a855e70be73c79dfb14617dab7fc8cf2ce86de29425b0be31e45c89d316.jpg)  
Fig. 7. Modeling process of the prediction model.

$$
X _ {i} = \frac {X _ {i} - X _ {\text {min}}}{X _ {\text {max}} - X _ {\text {min}}}\tag{4}
$$

## (2) Splitting the training set and test set

The training set and test set are divided using the “hold-out method” in this paper, with 80% of the data used for model training and 20% for model validation.

(3) LSTM neural network model parameter settings

The parameters that need to be set in the LSTM neural network model include [39]: the number of hidden layers, number of neurons in each layer, maximum number of network iterations. These parameters need to be flexibly adjusted based on the actual performance of the model. The L2 regularization parameter and learning rate are set to default values.

Considering the multitude of physical factors such as temperature, load, and cooling capacity in the prediction model, and the complex interrelationships among them in the time series, the mean squared error (MSE) is used to compare the fitting performance of the model under different settings of the number of hidden layers and the number of neurons in the LSTM model.

## (4) Model training

Based on the data set division and model parameter settings, the data set is read for model training. Using a sliding time window approach to predict cooling load or temperature for the next time step based on data from the previous five-time steps. By adjusting the model batch size and the number of epochs, training is stopped when the model loss function converges to prevent overfitting.

(5) Model validation and evaluation

Based on the aforementioned model parameter settings, the performance of the cooling load prediction model and the server room temperature prediction model is verified on both the training set and the test set.

The parameters of LSTM are adjusted by grid search and cross validation for several times [40]. Finally, the parameters of the two LSTM prediction models are shown in Table 2.

## 2.3. Evaluation indicators

The evaluation indexes used in the study are listed below.

This study uses the relative temperature fluctuation rate to measure the overall fluctuation amplitude of temperature in the selected time series. The lower relative fluctuation rate, the more stable temperature data. This was calculated using Eq. (5).

$$
R _ {\text { Temperature }} = \frac {T _ {\max} - T _ {\min}}{T _ {\text { ave }}} \times 100 \%\tag{5}
$$

where $R _ { T e m p e r a t u r e }$ is the relative temperature fluctuation rate; $T _ { m a x }$ is the temperature maximum value, $^ \circ \mathrm { C } ; T _ { m i n }$ is the temperature minimum value, $^ \circ \mathrm { C } ; T _ { a \nu e }$ is the temperature average value, $^ \circ \mathbf { C } .$

Table 2  
Parameters of LSTM prediction models.

<table><tr><td></td><td>Cooling load prediction model</td><td>Server room temperature prediction model</td></tr><tr><td>Feature vector dimension</td><td>5</td><td>7</td></tr><tr><td>Number of hidden layers</td><td>5</td><td>5</td></tr><tr><td>Number of neurons</td><td>1024</td><td>512</td></tr><tr><td>L2 regularization rate</td><td>0.01</td><td>0.01</td></tr><tr><td>Learning rate</td><td>0.001</td><td>0.001</td></tr><tr><td>Model optimizer</td><td>Adam</td><td>Adam</td></tr><tr><td>Loss function</td><td>MSE</td><td>MSE</td></tr><tr><td>Number of iterations</td><td>300</td><td>300</td></tr></table>

The integrated COP (ICOP) was used to evaluate the overall energy efficiency of a multi-chiller system, and the calculation formula is as follows:

$$
I C O P = (A \times C O P _ {s c r e w}) + (B \times C O P _ {m a g l e v})\tag{6}
$$

where A is the proportion of cooling supply from screw chiller; B is the proportion of cooling supply from magnetic levitation chiller; $C O P _ { s c r e w }$ is the average COP of screw chiller; $C O P _ { m a g l e \nu }$ is the average COP of magnetic levitation chiller.

The accuracy of the prediction model and the equipment model was evaluated using the goodness of fit R-Square $( \mathrm { R } ^ { 2 } )$ , mean relative error (MRE) [41], root mean square error (RMSE), mean square error (MSE), and mean absolute percentage error (MAPE). The calculation formulas are as follows [42]:

$$
R ^ {2} = 1 - \frac {\sum_ {i = 1} ^ {n} (y _ {i} - y _ {i} ^ {\prime})}{\sum_ {i = 1} ^ {n} (y _ {i} - y _ {\mathrm{mean}})} \times 100 \%\tag{7}
$$

$$
M R E = \frac {1}{n} \cdot \sum_ {i = 1} ^ {n} \left(\frac {y _ {i} - y _ {i} ^ {\prime}}{y _ {i}}\right)\tag{8}
$$

$$
R M S E = \sqrt {\frac {1}{n} \cdot \sum_ {i = 1} ^ {n} \left(y _ {i} - y _ {i} ^ {\prime}\right) ^ {2}}\tag{9}
$$

$$
M S E = \frac {1}{n} \cdot \sum_ {i = 1} ^ {n} \left(y _ {i} - y _ {i} ^ {\prime}\right) ^ {2}\tag{10}
$$

$$
M A P E = \frac {1}{n} \cdot \sum_ {i = 1} ^ {n} \frac {\left| y _ {i} - y _ {i} ^ {\prime} \right|}{y _ {i}} \times 100 \%\tag{11}
$$

where n is the number of samples; $y _ { i }$ is the actual value; $y _ { i } { \ ' }$ is the fitted value; $y _ { m e a n }$ is the actual mean value.

## 3. Case study

## 3.1. Case description

## 3.1.1. Data center description

The study was conducted in a data center in North China with a building area of about 8800 $\mathbf { m } ^ { 2 } { \mathrm { : } }$ , including six IDC rooms, as shown in Fig. 8, with a total area of 3000 m<sup>2</sup>. The building consists of six floors above ground and one basement floor. The first floor is the UPS room and maintenance duty center; the second floor is the service monitoring room and office area; the third to fifth floors are two IDC rooms with the same specifications and three computer room air conditioning (CRAC) rooms; the sixth floor is the power plant room, including cooling equipment such as chillers and pumps; and the first floor below ground is a diesel-powered generation room. The ambient temperature of the server room area is set at $2 2 { \pm } 2 ^ { \circ } \mathrm { C } ,$ , and the relative humidity is allowed to range from 30% to 60%. The server room is in the form of room-level downward-feeding and upward-returning cooling.

## 3.1.2. Operational system description

The cooling system of this data center adopts air-cooled chillers + CRAC end system. The cooling system mainly includes two sets of highpower screw air-cooled chiller units, two sets of low-power magnetic levitation air-cooled chiller units, four sets of circulating water pumps, and 30 sets of end precision air conditioners, and its detailed parameters are shown in Table 3.

## 3.1.3. Test method

This study adopts a combination of data center field testing and system operation log reading to collect the cooling system operation data and construct a data set for cooling load and temperature predic tion, equipment model modeling, and other work. The main collection

![](images/aba0a0d5f19100f5ef1f9b49e6593fc088b9010f83b6d62d195bd1dce17161b9.jpg)  
Fig. 8. IDC rooms site.

Table 3  
Data center cooling system equipment parameters information.

<table><tr><td>Serial number</td><td>Equipment name</td><td>Parameters</td><td>Quantity</td><td>Remarks</td></tr><tr><td>1</td><td>Screw air-cooled chiller</td><td>Rated cooling capacity: 886.0 kWRated power: 286.0 kWCooling performance factor: 3.03</td><td>2</td><td>Inverter chiller</td></tr><tr><td>2</td><td>Magnetic levitation air-cooled chiller</td><td>Rated cooling capacity: 350.0 kWRated power: 103.7 kWCooling performance factor: 3.38</td><td>2</td><td>Inverter chiller</td></tr><tr><td>3</td><td>Chilled water circulation pump</td><td>Flow rate: 121  $m^{3}/h$ Motor power: 18.5 kWRated speed: 1480 r/min</td><td>4</td><td>Inverter pumpThree uses, one backup</td></tr><tr><td>4</td><td>Server room precision air conditioning</td><td>Cooling capacity: 124.3 kWNumber of EC fans: 2EC fan air volume: 23600  $m^{3}/h$ EC fan rated power: 5.1 kW</td><td>30</td><td>Fan-fixed frequency operation</td></tr></table>

In this study, a multivariate polynomial regression model (MPRM) [43] was used to establish the performance-energy model of the chiller unit. The “equipment mathematical model database” is utilized for parameter identification. The MPRM is proposed by Lawrence Berkeley National Laboratory in the USA and involves the parameters of unit cooling capacity, chilled water return temperature, and cooling water return temperature. Its specific calculation formula is shown in Eq. (12). However, an air-cooled chiller does not have “cooling water”. According to the analysis of air-cooled and water-cooled chiller operation principle, in water-cooled units, the cooling water return temperature is the inlet temperature of the low-temperature side of the condenser, whereas in air-cooled units, it corresponds to the inlet temperature of the cool air, which is the ambient temperature [44]. Therefore, Eq. (12) can be improved to describe the relationship between the coefficient of performance (COP) of the air-cooled chiller unit and the unit cooling capacity, chilled water return temperature, and ambient temperature. The MPRM of the air-cooled chiller unit is shown in Eq. (13).

## 3.2. TRNSYS simulation platform construction

## 3.2.1. Equipment mathematical model

$$
\begin{array}{r l} C O P & = a _ {1} + a _ {2} \cdot Q _ {\text { cooling }} + a _ {3} \cdot T _ {\text { ei }} + a _ {4} \cdot T _ {\text { ci }} + a _ {5} \cdot Q _ {\text { cooling }} ^ {2} + a _ {6} \cdot T _ {\text { ei }} ^ {2} + a _ {7} \cdot T _ {\text { ci }} ^ {2} \\ & + a _ {8} \cdot Q _ {\text { cooling }} \cdot T _ {\text { ei }} + a _ {9} \cdot Q _ {\text { cooling }} \cdot T _ {\text { ci }} + a _ {1 0} \cdot T _ {\text { ei }} \cdot T _ {\text { ci }} \end{array}\tag{12}
$$

$$
\begin{array}{r l} C O P & = a _ {1} + a _ {2} \cdot Q _ {\text {cooling}} + a _ {3} \cdot T _ {\text {ei}} + a _ {4} \cdot T _ {a} + a _ {5} \cdot Q _ {\text {cooling}} ^ {2} + a _ {6} \cdot T _ {\text {ei}} ^ {2} + a _ {7} \cdot T _ {a} ^ {2} \\ & \quad + a _ {8} \cdot Q _ {\text {cooling}} \cdot T _ {\text {ei}} + a _ {9} \cdot Q _ {\text {cooling}} \cdot T _ {a} + a _ {1 0} \cdot T _ {\text {ei}} \cdot T _ {a} \end{array}
$$

and testing objects include data center cooling system operation data and IDC server room area thermal environment state parameters.

(13)

At the cooling system level, parameters such as ambient temperature, chilled water supply and return temperatures, flow rate, and equipment power need to be collected and tested. The main contents and methods of data collection and testing are shown in Table 4.

The thermal environment of the data center mainly includes the temperature and humidity of the cold aisle and hot aisle inside the computer room. Specific collection and testing contents and methods are shown in Table 5.

## Table 5

Information sheet for the collection and testing of the thermal environment system in the server room.

<table><tr><td>Serial number</td><td>Testing area</td><td>Acquisition/ testing content</td><td>Unit</td><td>Acquisition/ testing method</td><td>Acquisition frequency</td></tr><tr><td>1</td><td>Server room cold-aisle</td><td>Temperature Relative humidity</td><td>°C %</td><td>HOBO UX100-003 temperature and humidity</td><td>5 min</td></tr><tr><td>2</td><td>Server room hot-aisle</td><td>Temperature Relative humidity</td><td>°C %</td><td>sensor acquisition</td><td></td></tr></table>

Information sheet for the collection and testing of the cooling system in the server room.

<table><tr><td>Serial number</td><td>Equipment name</td><td>Acquisition/testing content</td><td>Unit</td><td>Acquisition/testing method</td><td>Acquisition frequency</td></tr><tr><td rowspan="4">1</td><td rowspan="4">Screw air-cooled chiller</td><td>Condenser ambient temperature</td><td>°C</td><td>TRM-ZS2 high-precision automatic weather station collection</td><td>20 min</td></tr><tr><td>Chilled water supply/return temperature</td><td>°C</td><td>BAS system readout</td><td>20 min</td></tr><tr><td>Chilled water flow</td><td> $m^3/h$ </td><td>TDS-100 series ultrasonic flowmeter measurement</td><td>5 min</td></tr><tr><td>Chiller power</td><td>kW·h</td><td>Switchboard readout</td><td>20 min</td></tr><tr><td rowspan="4">2</td><td rowspan="4">Magnetic levitation air-cooled chiller</td><td>Condenser ambient temperature</td><td>°C</td><td>TRM-ZS2 high-precision automatic weather station collection</td><td>20 min</td></tr><tr><td>Chilled water supply/return temperature</td><td>°C</td><td>BAS system readout</td><td>20 min</td></tr><tr><td>Chilled water flow</td><td> $m^3/h$ </td><td>TDS-100 series ultrasonic flowmeter measurement</td><td>5 min</td></tr><tr><td>Chiller power</td><td>kW·h</td><td></td><td>20 min</td></tr><tr><td rowspan="3">3</td><td rowspan="3">Chilled water circulation pump</td><td>Chilled water flow</td><td> $m^3/h$ </td><td>BAS system readout</td><td>20 min</td></tr><tr><td>Pump motor frequency</td><td>Hz</td><td>BAS system readout</td><td>20 min</td></tr><tr><td>Pump power</td><td>kW·h</td><td>Switchboard readout</td><td>20 min</td></tr><tr><td rowspan="4">4</td><td rowspan="4">Server room precision air conditioning</td><td>Fan speed</td><td>rpm</td><td>CRAC panel readout</td><td>1 h</td></tr><tr><td>Air velocity</td><td>m/s</td><td>FLUKE 925 impeller anemometer measurement</td><td>1 h</td></tr><tr><td>Supply air temperature</td><td>°C</td><td>HOBO UX100-003 temperature and humidity sensor acquisition</td><td>5 min</td></tr><tr><td>Precision air conditioning current</td><td>A</td><td>Switchboard measurement</td><td>1 h</td></tr></table>

Table 6  
Chiller COP regression parameters table.

<table><tr><td></td><td>Screw unit 1</td><td>Screw unit 2</td><td>Maglev unit 1</td><td>Maglev unit 2</td></tr><tr><td> $a_1$ </td><td>-986.27</td><td>84.22</td><td>-71.64</td><td>48.82</td></tr><tr><td> $a_2$ </td><td>-0.0566</td><td>-0.0317</td><td>0.1141</td><td>-0.0312</td></tr><tr><td> $a_3$ </td><td>140.2582</td><td>-9.9893</td><td>9.8601</td><td>-5.5709</td></tr><tr><td> $a_4$ </td><td>0.2978</td><td>0.0903</td><td>-0.3943</td><td>-0.1786</td></tr><tr><td> $a_5$ </td><td>-0.00000051</td><td>0.00000005</td><td>0.00005</td><td>-0.0001</td></tr><tr><td> $a_6$ </td><td>-5.0082</td><td>0.2959</td><td>-0.3264</td><td>0.1500</td></tr><tr><td> $a_7$ </td><td>-0.0002</td><td>0.0004</td><td>0.0024</td><td>0.0043</td></tr><tr><td> $a_8$ </td><td>0.0051</td><td>0.0026</td><td>-0.0058</td><td>0.0081</td></tr><tr><td> $a_9$ </td><td>-0.0004</td><td>-0.0001</td><td>-0.0008</td><td>-0.0006</td></tr><tr><td> $a_{10}$ </td><td>-0.0085</td><td>-0.0093</td><td>0.0184</td><td>-0.0056</td></tr></table>

where $a _ { 1 } { \sim } a _ { 1 0 }$ are the parameters to be recognized; $T _ { e i }$ is the chilled water return temperature, <sup>◦</sup>C; $T _ { c i }$ is the cooling water return temperature, $^ \circ \mathrm { C } ; T _ { a }$ is the ambient temperature, ${ } ^ { \circ } \mathbf { C } ; Q _ { c o o l i n g }$ is the cooling ca pacity, kW.

Although the four chillers in this study involve two models, their actual performance varies due to the wear and tear in the actual oper ation. In order to ensure the accuracy of the MPC, this study established mathematical models based on the actual operation data of four chiller respectively. A multiple linear regression method based on the least squares was used for parameter identification. A total of 900 sets of operational data were acquired for each chiller, of which 700 sets were used for model training and 200 sets for model validation. The regressed chiller COP fitting parameters are shown in Table 6.

The chilled water pumps in this study are configured as “three uses, one backup”, and they are all of the same model. According to the results of field measurements, the operating parameters of the three pumps are basically the same, so the same model is used for modeling the three pumps. Cumulative acquisition of 150 groups of data, of which 90 groups for model training, and 60 groups for model validation. Through the linear regression analysis of the least squares method, the mathematical model of the chilled water pump was obtained as shown in Eq. (14).

$$
P _ {\text { pump }} = 0. 0 2 3 8 \cdot M _ {e} ^ {2} - 5. 2 7 0 \cdot M _ {e} + 3 0 6. 7\tag{14}
$$

where $P _ { p u m p }$ is the pump power, kW; $M _ { e }$ is the chilled water flow rate, $\mathrm { { m ^ { 3 } / h } }$

Fig. 9 shows the comparison between the fitted and measured COP values for the four air-cooled chillers. As shown in the figure, the error rates between the fitted and measured values of the four models are basically within 5%. By employing Eq. (7) and Eq. (11) for error calculations in the mathematical models, the results are shown in Table 7. The accuracy and validity of the established unit performance-energy consumption mathematical models were successfully demonstrated.

## 3.2.2. Verification of simulation platform

In this study, TRNSYS 16 was used to build the simulation model of the data center cooling system. The constructed TRNSYS is shown in Fig. 10.

![](images/cdbfffde61b22da2ba538a1e2a0ab0d900333460ac426f84a85fe60cb9afd381.jpg)  
a) Screw unit 1 COP fitting error plot

![](images/fb172520f57f18aad04ee2ee349a296fd9366255bc311b5bb99a1f8ea558d803.jpg)  
c) Maglev unit 1 COP fitting error plot

![](images/74c209540200bea62d790f12d43098799d6a9a95e48c9f200f4c9757c927ae63.jpg)

b) Screw unit 2 COP fitting error plot  
![](images/496db72ffb295684ba2d92b181cc6d576974251f7611619718b15718e4a88f25.jpg)  
d) Maglev unit 2 COP fitting error plot  
Fig. 9. Error plot of COP fitting for four air-cooled chillers.

Table 7  
Evaluation of errors in mathematical models of chillers and water pumps.

<table><tr><td></td><td> $R^{2}$ </td><td>MAPE</td></tr><tr><td>Screw unit 1</td><td>95.8 %</td><td>1.34 %</td></tr><tr><td>Screw unit 2</td><td>90.2 %</td><td>2.00 %</td></tr><tr><td>Maglev unit 1</td><td>98.2 %</td><td>0.98 %</td></tr><tr><td>Maglev unit 2</td><td>98.5 %</td><td>0.94 %</td></tr><tr><td>Chilled water pumps</td><td>90.8 %</td><td>2.44 %</td></tr></table>

The TRNSYS simulation platform lays the foundation for future optimization and control system operation. To ensure the accuracy of the simulation results, it is necessary to validate the constructed TRNSYS model. In this study, the air-cooled chiller module, chilled water pump module, terminal room building module, and the entire cooling system were validated.

The built-in Type655 module in the Chiller unit of the TRNSYS TESS library was selected as the modeling construction of the air-cooled chiller. Parameter settings for the Type655 module in the Simulation Studio platform, the chilled water set temperature, chilled water return temperature, chilled water flow rate, and unit ambient temperature as inputs, and chiller power consumption and COP as monitoring parameters, as shown in Fig. 11.

The results of the comparison between the simulated and actual values of the Type655 model for the magnetic levitation and the screw air-cooled chiller over a continuous period of 72 h are shown in Fig. 12. By calculation, the MAPE of COP of magnetic levitation air-cooled chiller is 2.09% and the MAPE of power consumption is 2.06%; the MAPE of COP of screw air-cooled chiller is 1.44% and the MAPE of power consumption is 1.49%. Therefore. the established air-cooled chiller modules are accurate and can be used for further studies.

The Type110 variable speed water pump module in the Hydronics unit of the TRNSYS library was selected to model the chilled water pump. The Simulation Studio platform simulation interface is shown in Fig. 13.

The results of the comparison of the simulated and actual values of flow rate and power consumption of the chilled water pump TRNSYS simulation module Type110 over a continuous period of 72 h are shown in Fig. 14. The MAPE of the pump flow rate is calculated to be 0.93% and the MAPE of the pump power consumption is 2.23%. Therefore, the established chilled water pump model can be used for subsequent modeling simulation and analysis work.

The IDC room in this study consists of two parts, including the IDC room thermal environment and the end CRACs. Considering that the six IDC rooms in the data center have the same layout and are cooled by the same cooling system, multiple server rooms are combined to create a

![](images/ecb1f5f1d13fa6de452a5ff1a1948091344184ae512655dc33eab0a0214589dd.jpg)  
Fig. 10. TRNSYS simulation interface for the cooling system.

![](images/b5a1bc9436c96dcebe6441d48b3c9b0ca4fd00d747ca16d47859245dc60e0c14.jpg)  
Fig. 11. Type655 module simulation interface.

![](images/c1443bb53a02326201ad62c1efa496c30023ca5b920c0966ffd0a56075c466e4.jpg)

a) Validation of magnetic levitation air-cooled chiller module  
![](images/7d089bbb3e0815810c564892c96cedc524212d9c3c28fb34feb73e8268926de8.jpg)  
b) Validation of screw air-cooled chiller module

Fig. 12. Comparative validation of TRNSYS simulation data for two types of air-cooled chillers.  
![](images/2a1c09a861534103651ea34fd1a09e142c0bd79bbf102686da1f7adc9e2a6bf6.jpg)  
Fig. 13. Type110 module simulation interface.

single building model during modeling. The IT equipment cooling power is used as an internal heat source input parameter to reflect the real-time impact of dynamic changes in IT equipment loads on server room temperatures. Meanwhile, according to the working principle of CRACs, its main structure is the fan and water–air heat exchanger, and according to the actual test results. the fan speed and air volume on the indoor wind side are basically constant, so it is simplified to model as a heat exchanger with a constant flow rate on the wind side.

Type88 and Type91 modules were selected to model the thermal environment of IDC rooms and CRACs. The calculation principle of both modules is based on the energy conservation formula. Referring to the actual parameters of the building and the operating parameters of CRACs, the parameters of Type88 and Type91 modules were set respectively. The above modules were connected to the Simulation Studio platform, as shown in Fig. 15.

The simulation results of the above modules within 72 h are shown in Fig. 16. Through calculations, the MAPE for the server room temperature is 0.24% and the MAPE for the return water temperature is 0.74%, indicating that both models have high accuracy.

In this study, the IT load power, outdoor temperature, chilled water supply temperature, magnetic levitation unit. and screw unit chilled water flow rate are selected as input parameters and simulation calculations are carried out in 72 h to obtain the indoor temperature change curve of the IDC room and the total system power consumption value. The results are shown in Fig. 17.

![](images/057aa3500bd33a904ac93ba926fb98411f7db54bf309e122c0f2f50438077c36.jpg)  
Fig. 14. Comparative validation of TRNSYS simulation data for chilled water pumps.

![](images/fb560dbeb531ad5fc55d4b230878961d51595f76a767efaf6e1f5dd180830355.jpg)  
Fig. 15. Type88 and Type91 module simulation interface.

The average absolute error of the temperature of the computer room is 0.05<sup>◦</sup>C. Table 8 demonstrates the MAPE values of the day-by-day power consumption of each equipment. As can be seen from the table, the MAPE for each equipment is within 2%, and these results demonstrate the accuracy of the equipment module selection and parameter input in the developed TRNSYS model.

## 3.2.3. Comparative conditions

In this study, PID control, fuzzy control [45], and MPC control strategies were selected for comparative analysis.

![](images/fe016253ad349c9f3be3e89af56f22ee2d14d596754a2551a6ebddddec29b983.jpg)  
Fig. 16. Comparative validation of TRNSYS simulation data for IDC room module.

The PID control strategy based on the return water temperature to control the chilled water flow rate is used in the actual operation of the system. The fuzzy control strategy flow in this study is shown in Fig. 18, which is developed in the Simulink toolbox. The server room temperature and the server room temperature change rate are used as the input parameters of the fuzzy control strategy, and the chilled water flow rate control signal is used as the output parameter, so as to realize the synergistic control of the server room temperature and the chilled water system.

## 4. Results

## 4.1. Prediction models

## 4.1.1. Input parameters

4.1.1.1. Cooling load prediction model. Table 9 demonstrates the analyzed values of GRA between the cooling load and each factor to be screened at the moment of t+1. With GRA≥0.8 as the correlation judgment criterion, the time series of five factors, namely, the cooling supply $Q _ { c o o l i n g } ( t )$ at the previous moment, the server room temperature $T _ { i n } ( t )$ at the previous moment, the outdoor temperature $T _ { o u t } ( t - 1 )$ at the previous two moments, the IT equipment load $P _ { I T } ( t )$ at the previous moment, and the outdoor temperature $T _ { o u t } ( t )$ at the previous moment, are selected as the input feature vector of the cooling load prediction model.

4.1.1.2. Server room temperature prediction model. The GRA analysi results of the server room temperature prediction model are shown in Table 10. The input eigenvectors of the server room temperature prediction model are the cooling supply $Q _ { c o o l i n g } ( t )$ at the previous moment, the server room temperature $T _ { i n } ( t )$ at the previous moment. the water supply temperature $T _ { s u p p l y } ( t )$ at the previous moment, the IT equipment load $P _ { I T } ( t )$ at the previous moment, the outdoor temperature $T _ { o u t } ( t )$ at the previous moment, the cooling supply $Q _ { c o o l i n g } ( t { - } 1 )$ ) at the previous two moments and outdoor temperature $T _ { o u t } ( t + 1 )$ at the predicted time.

## 4.1.2. Accuracy verification

The samples for the cooling load and server room temperature prediction models consisted of 360 data sets from the cooling load and server room temperature databases, respectively. These data cover five working days, where the data time step is 20 min. In the whole dataset, 288 sets of data were used for model training, and 72 sets of data were used to validate the prediction performance of the built models, following an 8:2 ratio. The comparison of predicted and actual values of the built predictive model is shown in Fig. 19. Eqs. $( 7 ) - ( 9 )$ were used to evaluate the model prediction errors. Table 11 lists the results of the comparison of the evaluation metrics of the cooling load and the server room temperature prediction models. In terms of the overall fitting ef fect, both prediction models show good fitting performance on the training and test sets, with their goodness-of-fit $\bar { \mathsf { R } } ^ { 2 }$ exceeding 90%; the MRE and RMSE of the test set meet the ASHRAE standard [46]. Therefore, the established prediction models are accurate.

To further verify the effectiveness and necessity of the feature vector screening method based on the grey relational analysis, the original unscreened vectors and the screened input vectors were used as input parameters for training two prediction models, respectively. The training effects and single training durations of these models were compared. As shown in Table 12, optimizing the model input parameters by screening feature vectors has a minimal impact on the model fitting accuracy, but it can significantly reduce the model training time and improve the efficiency of prediction model.

Table 8  
MAPE value of daily power consumption of each equipment.

<table><tr><td></td><td>Magnetic levitation chiller</td><td>Screw chiller</td><td>Chilled water pump</td><td>Chilled water system</td></tr><tr><td>Day 1</td><td>0.11 %</td><td>1.81 %</td><td>0.09 %</td><td>1.32 %</td></tr><tr><td>Day 2</td><td>0.17 %</td><td>1.95 %</td><td>1.06 %</td><td>1.36 %</td></tr><tr><td>Day 3</td><td>0.83 %</td><td>1.98 %</td><td>0.20 %</td><td>1.37 %</td></tr><tr><td>Average value</td><td>0.37 %</td><td>1.91 %</td><td>0.45 %</td><td>1.35 %</td></tr></table>

![](images/8169cc77dfa8f659077d510e7b93d0981f9c584814947fbc03fbd3828cdfb2e8.jpg)

a) Comparison of simulated and actual server room temperatures  
![](images/08dc6bec7567c495548d9d723d236111912b42e88f3a774bceb84fae5bc227db.jpg)  
b) Comparison of simulated and actual values of total power consumption of each equipment  
Fig. 17. Comparison of TRNSYS simulated and actual values of server room temperature and equipment power consumption.

![](images/addf5073b9357f27d55086dae9d7c7be7ce09953132e64b79fe56497159a30f8.jpg)  
Fig. 18. Flowchart of fuzzy control strategy.

## 4.2. Comparison of control effects of three control strategies

Based on the TRNSYS model, three consecutive days were selected as typical days, with a simulation time interval of one hour, to compare the control effects of PID control, fuzzy control, and MPC control on the system.

## 4.2.1. Relative temperature fluctuation rate

The temperature variation curves of the server room under the three control strategies are shown in Fig. 20. From an overall trend perspec tive, the temperature variations under the three control methods are similar, especially between PID control and fuzzy control. Peaks in temperature occur around midday each day, with temperature troughs appearing around midnight. However, in the peak temperature regions, fuzzy control is better at suppressing the temperature rise in the server room compared to PID control, demonstrating relatively superior tem perature control effectiveness. Comparing these two control strategies, the server room temperature under the MPC control is significantly lower and closer to the target control temperature of 22<sup>◦</sup>C.

Table 9  
GRA analysis of cooling load predictors.

<table><tr><td>Factors to be screened</td><td>GRA analyzed value</td></tr><tr><td> $Q_{cooling}(t)$ </td><td>0.935</td></tr><tr><td> $T_{in}(t)$ </td><td>0.898</td></tr><tr><td> $T_{out}(t-1)$ </td><td>0.887</td></tr><tr><td> $P_{IT}(t)$ </td><td>0.881</td></tr><tr><td> $T_{out}(t)$ </td><td>0.865</td></tr><tr><td> $T_{out}(t+1)$ </td><td>0.792</td></tr><tr><td> $T_{in}(t-1)$ </td><td>0.766</td></tr><tr><td> $Q_{cooling}(t-1)$ </td><td>0.760</td></tr><tr><td> $P_{IT}(t-1)$ </td><td>0.676</td></tr></table>

Table 10  
GRA analysis of server room temperature predictors.

<table><tr><td>Factors to be screened</td><td>GRA analyzed value</td></tr><tr><td> $Q_{cooling}(t)$ </td><td>0.879</td></tr><tr><td> $T_{in}(t)$ </td><td>0.878</td></tr><tr><td> $T_{supply}(t)$ </td><td>0.847</td></tr><tr><td> $P_{IT}(t)$ </td><td>0.834</td></tr><tr><td> $T_{out}(t)$ </td><td>0.834</td></tr><tr><td> $Q_{cooling}(t-1)$ </td><td>0.826</td></tr><tr><td> $T_{out}(t+1)$ </td><td>0.821</td></tr><tr><td> $T_{in}(t-1)$ </td><td>0.790</td></tr><tr><td> $T_{supply}(t-1)$ </td><td>0.787</td></tr><tr><td> $T_{out}(t-1)$ </td><td>0.783</td></tr><tr><td> $P_{IT}(t-1)$ </td><td>0.713</td></tr></table>

Table 13 shows the typical characteristic values of the temperature data under the three control strategies. From the table, it can be seen that the average temperature in the server room is lowest under the MPC strategy. Additionally, the maximum temperature value is the smallest among the three control methods, indicating that the MPC strategy can significantly reduce the temperature in the IDC room and better meet the temperature control requirements of the server room. Further analyze the temperature fluctuation characteristics under the three control methods. In terms of temperature extreme and standard deviation, PID control shows the highest values, while MPC control shows the lowest values consistently. Fuzzy control slightly lags behind MPC control in these metrics. Taking the temperature standard deviation as an indicator of temperature stability, comparing PID and fuzzy control strategies, the temperature stability under MPC is improved by 27.77% and 18.08%, respectively. The above data all indicate that temperature stability is best under MPC, which is beneficial for providing a stable operating environment for IT equipment in the server room.

The temperature performance is further analyzed by combining the logic of the three control strategies. In the MPC logic, a clear control objective is set, which is to maintain the temperature at 22<sup>◦</sup>C. Additionally, there is also a feedback mechanism that effectively incorporates the previous control deviation into the next prediction and optimization process, ensuring that the optimization and control results consistently approach the control objective. At the same time, the prediction model can predict the potential future state of the system and make decisions in advance for potential increases in cooling demand. It proactively increases the cooling capacity to prevent temperature rise in the data center, thus achieving control over temperature stability. In comparison, PID control employs a feedback control without a prediction model. Due to the thermal transfer and distribution lag issues in the data center cooling system, it takes a while for the return water temperature to change after the current control parameters are executed, and the feedback signal that the system actually receives is also delayed. Only after the cooling load in the server room increases and the room temperature rises, the return temperature of the chilled water will increase, prompting the system to issue a command to increase the cooling capacity. In PID control, the supply of cooling capacity always lags behind the demand, leading to significant temperature fluctuations. The average level of temperature control lacks a clear target, and its effectiveness depends on the tuning of the system. In this study, the fuzzy control is optimized and improved based on data from PID control results. Its control rule logic places particular emphasis on conditions where temperatures are high in the data center. When the server room temperature is elevated, the flow rate of chilled water is significantly increased. Therefore, compared to PID control during temperature peak periods, fuzzy control demonstrates better capability in suppressing temperature rises. In conclusion, MPC strategy outperforms PID control and fuzzy control in data center temperature regulation. It achieves lower average temperature control and better stability.

## 4.2.2. Operational energy consumption of the chilled-water system

Under the three comparison conditions, the energy consumption of the simulated chilled-water system (4 chillers + 3 pumps) was evaluated. Over a 3-day period, the total system energy consumption was 36,096.9 kW⋅h for the PID control, 34,443.5 kW⋅h for the fuzzy control, and 31,833.3 kW⋅h for the MPC control. Among them, the MPC strategy is the most energy-efficient, saving 11.81% compared to the PID control and 7.58% compared to the fuzzy control.

Fig. 21 shows the daily power consumption comparison of the three control strategies. From the graph, it is evident that the daily power consumption over the three days shows a gradual decrease trend. Within each day, the MPC strategy consistently exhibits the lowest energy consumption. Compared to PID control, daily energy savings are around 12%, and compared to fuzzy control, they are around 8%. These savings align with the overall 3-day energy efficiency, indicating that MPC strategy also possesses excellent and stable capability to reduce system energy consumption on a daily basis.

Fig. 22 shows the hourly variation of the total energy consumption for three control strategies from 0:00 on a certain day to 0:00 the next day. From the graph, it is evident that under the three control strategies, the total energy consumption of the system exhibits a characteristic of first decreasing and then increasing, followed by a decrease again, indicating that each control strategy has two turning points of energy consumption changes within the day, corresponding to the daily trough and peak points. The valley turning points for the PID and fuzzy control strategies occur at 8 a.m., while the MPC strategy valley turning point is at 6 a.m. At around 11 a.m., the energy consumption under the PID control enters the peak range and starts to decrease around 7p.m., considering 7p.m. as the peak turning point for the PID control. The energy consumption under the fuzzy control reaches its peak in the evening around 6p.m. In contrast, the energy consumption under the MPC strategy reaches its peak turning point at around 1p.m. and grad ually starts to decline. Therefore, whether at the trough or peak points of energy consumption, the MPC strategy consistently appears earliest, indicating the effectiveness of the “prediction” component in MPC.

Further combining with temperature data analysis, the outdoor temperature gradually begins to rise at 7 a.m., followed by an increase in the server room temperature under PID and fuzzy control at 8 a.m., in line with the outdoor temperature increase. Similarly, at 6p.m., as the outdoor temperature starts to decrease, the server room temperature under the two control methods also starts to decrease. In the process of

Table 11  
Comparison of evaluation indicators of prediction models.

<table><tr><td></td><td>Cooling load prediction model</td><td>Server room temperature prediction model</td></tr><tr><td rowspan="2"> $R^2$ </td><td>96.0 % (training set)</td><td>93.5 % (training set)</td></tr><tr><td>93.9 % (test set)</td><td>92.5 % (test set)</td></tr><tr><td>MRE</td><td>0.17 %</td><td>0.04 %</td></tr><tr><td>RMSE</td><td>2.38</td><td>0.02</td></tr></table>

The training effects and durations of prediction models under different feature vector inputs.

<table><tr><td colspan="2"></td><td>Training set R2</td><td>Single training duration(s)</td></tr><tr><td rowspan="2">Cooling load prediction model</td><td>Unscreened feature vectors</td><td>96.5%</td><td>229.9</td></tr><tr><td>Screened feature vectors</td><td>96.0%</td><td>164.5</td></tr><tr><td rowspan="2">Server room temperature prediction model</td><td>Unscreened feature vectors</td><td>94.7%</td><td>176.7</td></tr><tr><td>Screened feature vectors</td><td>93.5%</td><td>119.5</td></tr></table>

![](images/1e4dcf47406e876c36427a66434cef76db1ddb6f7be4b3cba1e630fe8d03b5fc.jpg)  
a) Comparison of predicted and actual cooling loads

![](images/1dc52a591de9c333ca089a5df703052dc961d2753bc567ca19113d1ef836fe7a.jpg)  
b) Comparison of predicted and actual server room temperatures.  
Fig. 19. Comparison of predicted and actual values of the LSTM model.

![](images/33fc9731e3d93ab20a777838fdd93f9835c7603da26399264b342ebcea97bec8.jpg)  
Fig. 20. Temperature variation curve of server room under different control strategies.

Table 13  
Typical eigenvalues of temperature data under different control strategies.

<table><tr><td></td><td>PID control</td><td>Fuzzy control</td><td>MPC control</td></tr><tr><td>Average value /°C</td><td>22.77</td><td>22.75</td><td>22.61</td></tr><tr><td>Maximum value /°C</td><td>22.93</td><td>22.90</td><td>22.73</td></tr><tr><td>Extremely poor /°C</td><td>0.42</td><td>0.36</td><td>0.31</td></tr><tr><td>Relative volatility</td><td>1.83 %</td><td>1.58 %</td><td>1.37 %</td></tr><tr><td>Standard deviation /°C</td><td>0.0922</td><td>0.0813</td><td>0.0666</td></tr></table>

![](images/85561befa10e5589fc9f8cd8dfa86535d6f19383b462c0cbf13455251ea3fa7d.jpg)  
Fig. 21. Comparison of daily power consumption for different control strategies.

predicting future cooling load and server room temperature, the MPC strategy captures the potential impact of outdoor temperature changes on cooling load and server room temperature through neural networks. It “preemptively” starts to adjust control parameters to suppress fluctuations in server room temperature caused by increases or decreases in outdoor temperature. For example, in the MPC strategy, the power of the cooling system is increased at 6 a.m., prior to the moment when the outdoor temperature starts to rise, thereby ensuring that the server room temperature remains stable around 22.63<sup>◦</sup>C in the subsequent period.

## 4.2.3. Energy efficiency of cooling source equipment

From the perspective of different types of equipment, the power consumption of each control strategy is disaggregated into three categories: screw chillers, magnetic levitation chillers, and pumps. The distribution and proportions of power consumption are shown in Fig. 23 and Fig. 24, respectively. In the graph, it is clear that the power consumption structure of various equipment under both PID control and fuzzy control is quite similar, with the screw chillers accounting for about 74%, and the magnetic levitation chillers accounting for about 16%. Under the MPC strategy, the proportion of the screw chillers de creases to 58.9%, while the power consumption of the magnetic levi tation chillers increases to 30.9%. The proportion of the pumps power consumption is similar to the other two control strategies, around 10%.

Fig. 25 and Fig. 26 show the variation of cooling capacity and COP of the two types of chillers under the three control strategies for 24 h on a certain day, respectively. From the graph, it can be seen that the differences in cooling capacity and COP between the two types of chillers under PID control and fuzzy control are relatively small, while the cooling capacity and COP of the chillers under MPC strategy show significant differences compared to the other two control strategies. Under the MPC strategy, the cooling capacity of the screw chiller is significantly lower than that of PID control and fuzzy control. Compared to PID control, the average cooling supply was approximately reduced by 34.9 kW⋅h. On the other hand, the cooling capacity of the magnetic levitation chiller is significantly higher than the other two control strategies. Compared to PID control, the average cooling supply increased by approximately 38.1 kW⋅h. In the PID control, the proportion of cooling capacity for screw chiller and magnetic levitation chiller is 71.6% and 28.4%, respectively. However, in the MPC strategy, the ratio between the two is 65.1% and 34.9%. Comparing the screw chiller COP, the average COP of chiller under MPC strategy is about 2.86, significantly higher than that of PID (the average COP is about 2.18) and fuzzy control (the average COP is about 2.22). In contrast to the COP performance of screw chiller, under the MPC strategy, the average COP of magnetic levitation chiller is lower than PID (the average COP is about 3.09) and fuzzy control (the average COP is about 3.20), at only 2.73.

The variation of the ICOP under different control strategies is shown in Fig. 27. Taking PID control as a comparison case for analysis, under the MPC strategy, the cooling supply proportion of the screw chiller decreased, but the COP increased by 31.2%. For the magnetic levitation chiller, the cooling supply proportion increased, but the COP decreased by 11.7%. Even though the energy consumption percentage of the magnetic levitation chiller increased, its cooling supply proportion is still lower than that of the screw chiller. Actually, the increase in COP of the screw chiller is greater than the decrease in COP of the magnetic levitation chiller. Therefore, the ICOP under the MPC strategy increased by 15.2%, with an average value of approximately 2.81.

The comparison of the total cooling capacity and ICOP of the chiller under different control strategies is shown in Table 14. As seen in the table, compared to the other two control strategies, the total cooling capacity of the system under the MPC strategy only increases a little more. However, the ICOP increases more significantly by adjusting the operating parameters and cooling supply proportion of the two types of chillers.

![](images/49a4982a57dc8a4ce0ecdb2057efbc91ecdb351389219c6a5bb41297809fc815.jpg)  
Fig. 22. Relationship between intra-day power consumption, server room temperature, and outdoor temperature change under different control strategies.

![](images/9fcf99ff71c97abd22bad904d9b41766b3e7d82d0fb30e89882fed0d0f6aee21.jpg)  
Fig. 23. Distribution of power consumption of each equipment under different control strategies.

Combining the energy consumption of the two types of chillers with the MPC algorithm logic for the multi-chiller system in data centers, it can be analyzed that, compared to the other two control strategies, the MPC strategy includes a rolling optimization process. One of its optimization objectives is to minimize the energy consumption of the cooling system. Therefore, the optimization results aim for the lower total system energy consumption rather than minimizing the energy consumption of a single device. The PSO algorithm is used to optimize the configuration combination of chilled water flow rate and supply water temperature to achieve the best operation conditions for energy consumption and room temperature control effectiveness, which opti mized the proportion of cooling capacity provided by different chillers in the system. This results in a reduction in total energy consumption of the cooling system and an improvement in the efficiency of the chilled water system.

## 4.2.4. System power usage effectiveness

The daily PUE values for the data center under the three control strategies are shown in Table 15. In comparison with the other two control strategies, under MPC strategy, the PUE value is significantly reduced. Compared to PID control, the highest daily PUE value decreases by 0.06. Considering that the simulation condition is summer condition, which is the period with the highest PUE value in a year, and the data center is located in North China utilizing air-cooled chillers, natural cooling sources can be used during the transitional seasons to further reduce the PUE values. Referring to the case of data centers located in neighboring regions with similar climates and the same system form, the PUE of summer working conditions is generally 1.1 times the annual PUE. Therefore, it is projected that the annual PUE of the data center in this study after adopting the MPC scheme is about 1.42, which basically meets the technical requirements in the “Guidance on Strengthening the Construction of Green Data Centers”.

![](images/e2f3baa4536e57630226eba17b4fa707410ccd99c40b42e17372bcd7c8a2a2ab.jpg)  
Fig. 24. Percentage of power consumption of each equipment under different control strategies.

![](images/6fdeaa61a7d88dd767bcde76394b29197de3e62fc9b6c5cd297db91a1c05d90a.jpg)  
Fig. 25. Cooling capacity and COP of single screw chiller under different control strategies.

## 5. Discussion

To address the issue of how temperature control in data center can follow the sequential change patterns of internal and external disturbance variables, and to achieve precise dynamic intelligent control, this study proposes a MPC strategy for coordinated configuration of multiple chillers based on the overall system energy conservation constraint. Considering that the IDC room in this study has no external windows and operates unattended, it is believed that the data center has negligible latent heat load. Therefore, this study disregards the impact of relative humidity variations and focuses on the relationship between cooling load and server room temperature. The MPC algorithmic structure coupled with cooling load prediction model and server room temperature prediction model. The difference between the predicted temperature of the server room and the set temperature was taken as one of the optimization obiectives to achieve accurate control of the server room temperature. The total cooling capacity provided by the chillers is

![](images/430c438a3ddd96d9a87bee905e03ebe95bad6058c4f535eec786dcc700431c79.jpg)  
Fig. 26. Cooling capacity and COP of a single maglev chiller under different control strategies.

![](images/28a5be08fb3fad3b30cbfb34f3ff3b95e6b702fd4c6096d7f91d0134a5ff8e4e.jpg)  
Fig. 27. Variation of ICOP for chillers under different control strategies.

Table 14  
Comparison of cooling capacity and ICOP of the chillers under different control strategies.

<table><tr><td></td><td>Cooling (kW·h)</td><td>ICOP</td></tr><tr><td>PID control</td><td>1127.60</td><td>2.44</td></tr><tr><td>Fuzzy control</td><td>1121.19</td><td>2.50</td></tr><tr><td>MPC control</td><td>1133.90</td><td>2.81</td></tr></table>

equal to the predicted value of the cooling load to achieve energy-saving operation of the cooling system. Based on the prediction models and the performance-energy consumption mathematical models of the cooling system, the PSO algorithm is used to solve the combination of chilled water flow rate and temperature configurations of multiple chillers to meet the control objectives, reducing the cooling system operating energy consumption, and then reducing the PUE of the data center.

Table 15  
Daily PUE values under different control strategies

<table><tr><td></td><td>PID control</td><td>Fuzzy control</td><td>MPC control</td></tr><tr><td>Day 1</td><td>1.63</td><td>1.61</td><td>1.58</td></tr><tr><td>Day 2</td><td>1.62</td><td>1.60</td><td>1.56</td></tr><tr><td>Day 3</td><td>1.60</td><td>1.58</td><td>1.54</td></tr><tr><td>Full year (extrapolated)</td><td>1.47</td><td>1.45</td><td>1.42</td></tr></table>

This study proposes an intelligent control strategy for the joint operation of multiple chillers from the perspective of a chilled-water system. Building on this, future research could extend this focus to encompass the whole cooling system, integrating both chilled water and air systems. By regulating water temperature and flow in the chilledwater system, as well as air temperature and speed in the air system, more precise control of the thermal environment and energy consumption in data centers can be achieved.

## 6. Conclusion

This study focused on establishing a MPC strategy for a multi-chiller system in a data center considering the constraint of whole system energy conservation. It takes the balance between supply and demand of cooling capacity as a constraint, while considering the precise control of the server room temperature, to realize the collaborative configuration of multiple chillers. It solves the problem of imprecise control and high energy consumption of cooling systems with multiple chillers. The conclusions of this study are as follows:

(1) The cooling load and server room temperature prediction models based on LSTM neural network considering IT equipment load were established, with MRE of 0.17% and 0.04%, and RMSE of 2.38 and 0.02, respectively. This proves the strong predictive performance of the models and provides an implementation method for cooling load and temperature prediction in data centers.

(2) An optimization algorithm framework for cooling capacity allocation in a multi-chiller system was established based on the energy conservation constraints of the whole system. The algorithm employs an optimization strategy to determine the combination of chilled water temperature and flow rates for multiple chillers. In this study, after optimizing with the model algorithm, the cooling capacity provided by the screw chiller decreased from 71.6% to 65.1%, and its energy consumption proportion decreased from 73.6% to 58.9%. The cooling capacity provided by the magnetic levitation chiller increased from 28.4% to 34.9%, and its energy consumption proportion increased from 17.5% to 30.9%.

(3) The MPC method for the multi-chiller system in data center achieves multi-objective optimization control of server room temperature deviation and chilled-water system energy consumption. Compared to PID control, it reduces the server room temperature by approximately 0.16<sup>◦</sup>C, improves the temperature stability by 27.8%, and simulta neously reduces the system energy consumption by about 11.8%. Compared to fuzzy control, it reduces the server room temperature by about 0.14<sup>◦</sup>C, improves the temperature stability by 18.1%, and simultaneously reduces the system energy consumption by about 7.6%. Comprehensive comparison, the MPC can achieve a stable reduction of server room temperature while reducing the power consumption of the cooling system, proving its dynamic regulation effectiveness in data center cooling scenarios.

## CRediT authorship contribution statement

Jing Zhao: Writing – review & editing, Funding acquisition, Conceptualization. Ziyi Chen: Writing – original draft, Methodology, Investigation. Haonan Li: Methodology, Investigation. Dehan Liu: Writing – review & editing.

## Declaration of competing interest

The authors declare that they have no known competing financial interests or personal relationships that could have appeared to influence the work reported in this paper.

## Acknowledgment

The work is supported by the Natural Science Foundation of China (No. 51678398).

## Data availability

The authors do not have permission to share data.

## References

[1] Y. Liu, X. Wei, J. Xiao, Z. Liu, Y. Xu, Y. Tian, Energy consumption and emission mitigation prediction based on data center traffic and PUE for global data centers, Glob. Energy Interconnect. 3 (2020) 272–282, https://doi.org/10.1016/j. gloei.2020.07.008.

[2] E. Masanet, A. Shehabi, N. Lei, S. Smith, J. Koomey, Recalibrating global data center energy-use estimates, Science 367 (2020) 984–986, https://doi.org/ 10.1126/science.aba3758

[3] S. Zou, Q. Zhang, C. Yue, Comparative study on different energy-saving plans using water-side economizer to retrofit the computer room air conditioning system, J. Build. Eng. 69 (2023) 106278, https://doi.org/10.1016/j.jobe.2023.106278.

[4] F. Zhou, C. Shen, G. Ma, X. Yan, Power usage effectiveness analysis of a liquidpump-driven hybrid cooling system for data centers in subclimate zones, Sustain. Energy Technol. Assess. 52 (2022) 102277, https://doi.org/10.1016/j. seta.2022.102277.

[5] A. Habibi Khalaj, S.K. Halgamuge, A Review on efficient thermal management of air- and liquid-cooled data centers: From chip to the cooling system, Appl. Energy 205 (2017) 1165–1188. 10.1016/j.apenergy.2017.08.037.

[6] Y. Wang, X. Bai, Y. Fu, Y. Tang, C. Jin, Z. Li, Field experiment and numerical simulation for airflow evaluation in a data center with row-based cooling, Energy Build. 294 (2023) 113231, https://doi.org/10.1016/j.enbuild.2023.113231.

[7] Y. Zhu, Q. Zhang, L. Zeng, J. Wang, S. Zou, An advanced control strategy of hybrid cooling system with cold water storage system in data center, Energy (2024) 130304, https://doi.org/10.1016/j.energy.2024.130304.

[8] C. Nadjahi, H. Louahlia, S. Lemasson, A review of thermal management and innovative cooling strategies for data center, Sustain. Comput. Inform. Syst. 19 (2018) 14–28, https://doi.org/10.1016/j.suscom.2018.05.002.

[9] B. Durand-Estebe, C. Le Bot, J.N. Mancos, E. Arquis, Data center optimization using PID regulation in CFD simulations, Energy Build. 66 (2013) 154–164, https://doi org/10.1016/i.enbuild.2013.07.053.

[10] C.-L. Chen, Y.-C. Chang, T.-S. Chan, Applying smart models for energy saving in optimal chiller loading, Energy Build. 68 (2014) 364–371, https://doi.org/ 10.1016/i enbuild 2013.04 030

[11] L. Yao, J.-H. Huang, Multi-objective optimization of energy saving control for air conditioning system in data center, Energies 12 (2019) 1474, https://doi.org 10.3390/en12081474.

[12] W. Zhang, Y. Wen, Y. Wah Wong, K. Chuan Toh, C.-H. Chen, Towards join optimization over ICT and cooling systems in data centre: a survey, IEEE Commun. Surv. Tutor. 18 (2016) 1596–1616, https://doi.org/10.1109/ COMST 2016.2545109

[13] N. Aste, M. Manfren, G. Marenzi, Building Automation and Control Systems and performance optimization: a framework for analysis, Renew. Sustain. Energy Rev. 75 (2017) 313–330, https://doi.org/10.1016/j.rser.2016.10.072

[14] J. Liao, X. Xie, H. Nemer, D.E. Claridge, C.H. Culp, A simplified methodology to optimize the cooling tower approach temperature control schedule in a cooling system, Energy Convers. Manag. 199 (2019) 111950, https://doi.org/10.1016/j. enconman.2019.111950

[15] J. Zhao, D. Liu, X. Yuan, P. Wang, Model predictive control for the ice-storage airconditioning system coupled with multi-objective optimization, Appl. Therm. Eng. 243 (2024) 122595, https://doi.org/10.1016/j.applthermaleng.2024.122595.

[16] J. Zhao, Z. Yang, L. Shi, D. Liu, H. Li, Y. Mi, H. Wang, M. Feng, T.J. Hutagaol, Photovoltaic capacity dynamic tracking model predictive control strategy of air conditioning systems with consideration of flexible loads, Appl. Energy 356 (2024) 122430, https://doi.org/10.1016/j.apenergy.2023.122430.

[17] J. Zhao, L. Shi, J. Li, H. Li, Q. Han, A model predictive control regulation model for radiant air conditioning system based on delay time, J. Build. Eng, 62 (2022 105343. https://doi.org/10.1016/i.iobe.2022.105343.

[18] Y. Yao. D.K. Shekhar. State of the art review on model predictive control (MPC) in Heating Ventilation and Air-conditioning (HVAC) field, Build. Environ. 200 (2021) 107952, https://doi.org/10.1016/j.buildenv.2021.107952.

[19] J. Zhao, J. Li. Y. Shan. Research on a forecasted load-and time delav-based model predictive control (MPC) district energy system model. Energy Build. 231 (2021) 110631, https://doi.org/10.1016/i.enbuild.2020.110631.

[20] F. Smarra, A. Jain, T. De Rubeis, D. Ambrosini, A. D’Innocenzo, R. Mangharam, Data-driven model predictive control using random forests for building energy optimization and climate control, Appl. Energy 226 (2018) 1252–1272, https:// doi.org/10.1016/j.apenergy.2018.02.126.

[21] N.S. Raman, B. Chen, P. Barooah, On energy-efficient HVAC operation with Model Predictive Control: a multiple climate zone study, Appl. Energy 324 (2022) 119752, https://doi.org/10.1016/j.apenergy.2022.119752.

[22] Y. Chen, J. Gao, J. Yang, U. Berardi, G. Cui, An hour-ahead predictive control strategy for maximizing natural ventilation in passive buildings based on weather forecasting, Appl. Energy 333 (2023) 120613, https://doi.org/10.1016/j. apenergy,2022.120613

[23] M. Farrokhifar, H. Bahmani, B. Faridpak, A. Safari, D. Pozo, M. Aiello, Model predictive control for demand side management in buildings: a survey, Sustain. Cities Soc, 75 (2021) 103381, https://doi,org/10.1016/i.scs,2021.103381

[24] Y. Zhu, Q. Zhang, L. Zeng, J. Wang, S. Zou, H. Zheng, An advanced control strategy for optimizing the operation state of chillers with cold storage technology in data

center, Energy Build. 301 (2023) 113684, https://doi.org/10.1016/j. enbuild.2023.113684.

[25] K. Wang, L. Ye, S. Yang, Z. Deng, J. Song, Z. Li, Y. Zhao, A hierarchical dispatch strategy of hybrid energy storage system in internet data center with model predictive control, Appl. Energy 331 (2023) 120414, https://doi.org/10.1016/j. apenergy.2022.120414.

[26] M. Ogawa, H. Endo, H. Fukuda, H. Kodama, T. Sugimoto, H. Soneda, M. Kondo, Cooling control restraining effects due to ICT equipment utilization of disturbance based on model predictive control for modular data center. in: 2014 IEEE Conf. Control Appl. CCA, IEEE, Juan Les Antibes, France, 2014: pp. 183–190. 10.1109/ CCA.2014.6981349

[27] B.R. Park, Y.J. Choi, E.J. Choi, J.W. Moon, Adaptive control algorithm with a retraining technique to predict the optimal amount of chilled water in a data center cooling system, J. Build. Eng. 50 (2022) 104167, https://doi.org/10.1016/j. jobe.2022.104167.

[28] M. Zapater, J.L. Risco-Martín, P. Arroba, J.L. Ayala, J.M. Moya, R. Hermida, Runtime data center temperature prediction using Grammatical Evolution techniques, Appl. Soft Comput. 49 (2016) 94–107, https://doi.org/10.1016/j. asoc.2016.07.042.

[29] Y. Tarutani, K. Hashimoto, G. Hasegawa, Y. Nakamura, T. Tamura, K. Matsuda, M. Matsuoka, Temperature Distribution Prediction in Data Centers for Decreasing Power Consumption by Machine Learning, in: 2015 IEEE 7th Int. Conf. Cloud Comput. Technol. Sci. CloudCom, IEEE, Vancouver, BC, Canada, 2015: pp. 635–642. 10.1109/CloudCom.2015.49.

[30] J. Drgona,ˇ D. Picard, M. Kvasnica, L. Helsen, Approximate model predictive building control via machine learning, Appl. Energy 218 (2018) 199–216, https:/ doi.org/10.1016/j.apenergy.2018.02.156.

[31] J. Arroyo, C. Manna, F. Spiessens, L. Helsen, Reinforced model predictive control (RL-MPC) for building energy management, Appl. Energy 309 (2022) 118346, https://doi.org/10.1016/j.apenergy.2021.118346.

[32] ASHRAE TC 9.9, Thermal Guidelines for Data Processing Environments–Expanded Data Center Classes and Usage Guidance: 2011.

[33] Y. Huang, L. Shen, H. Liu, Grey relational analysis, principal component analysis and forecasting of carbon emissions based on long short-term memory in China, J. Clean. Prod. 209 (2019) 415–423, https://doi.org/10.1016/j. iclepro.2018.10.128.

[34] Y. Wang, S. Sun, G.G. De Oliveira, Short-term electricity price prediction using grey relation analysis, SVM, and amended squirrel search optimizer, J. Electr. Eng. Technol. 19 (2024) 2059–2075, https://doi.org/10.1007/s42835-023-01686-7.

[35] R. Çakıroglu,˘ O.K. Çinici, S¸ . Asal, A. Acır, Multi-objective optimization of the hydrogen fuel production of a solar-based cobalt-chlorine (Co–Cl) thermochemical

cycle with grey relational analysis, Int. J. Hydrog. Energy 68 (2024) 360–373, https://doi.org/10.1016/j.ijhydene.2024.04.219.

[36] G. Wu, Y.-C. Hu, Y. Chiu, P. Jiang, R. Chi, Forecast combination using grey relational analysis and Choquet fuzzy integral for container throughput forecasting, Expert Syst. Appl. 252 (2024) 124170, https://doi.org/10.1016/j. eswa.2024.124170.

[37] R. Chalapathy, N.L.D. Khoa, S. Sethuvenkatraman, Comparing multi-step ahead building cooling load prediction using shallow machine learning and deep learning models, Sustain. Energy Grids Netw. 28 (2021) 100543, https://doi.org/10.1016/j. segan.2021.100543.

[38] Z. Wang, T. Hong, M.A. Piette, Building thermal load prediction through shallow machine learning and deep learning, Appl. Energy 263 (2020) 114683, https://doi. org/10.1016/j.apenergy.2020.114683

[39] Z. Hu, Y. Gao, S. Ji, M. Mae, T. Imaizumi, Improved multistep ahead photovoltaic power prediction model based on LSTM and self-attention with weather forecast data, Appl. Energy 359 (2024) 122709, https://doi.org/10.1016/j. apenergy,2024.122709

[40] A. Zhao, Y. Zhang, Y. Zhang, H. Yang, Y. Zhang, Prediction of functional zones cooling load for shopping mall using dual attention based LSTM: A case study, Int. J. Refrig. 144 (2022) 211–221, https://doi.org/10.1016/j.ijrefrig.2022.07.020

[41] J. Zhao, X. Yuan, Y. Duan, H. Li, D. Liu, An artificial intelligence (AI)-driven method for forecasting cooling and heating loads in office buildings by integrating building thermal load characteristics, J. Build. Eng. 79 (2023) 107855, https://doi. org/10.1016/i,jobe.2023.107855.

[42] J. Xing, H. Wang, K. Luo, S. Wang, Y. Bai, J. Fan, Predictive single-step kinetic model of biomass devolatilization for CFD applications: a comparison study of empirical correlations (EC), artificial neural networks (ANN) and random fores (RF), Renew. Energy 136 (2019) 104–114, https://doi.org/10.1016/j. renene.2018.12.088.

[43] F.W.H. Yik, V.K.C. Lam, Chiller models for plant design studies, Build. Serv. Eng. Res. Technol. 19 (1998) 233–241, https://doi.org/10.1177/ 014362449801900407

[44] B. Yue, B. Su, F. Xiao, A. Li, K. Li, S. Li, R. Yan, Q. Lian, A. Li, Y. Li, X. Fang, X. Liang, Energy-oriented control retrofit for existing HVAC system adopting data driven MPC – Methodology, implementation and field test, Energy Build. 295 (2023).113286. https://doi,org/10.1016/i.enbuild.2023.113286

[45] Y. Du, Z. Zhou, J. Zhao, Multi-regional building energy efficiency intelligent regulation strategy based on multi-objective optimization and model predictive control, J. Clean. Prod. 349 (2022) 131264, https://doi.org/10.1016/j. jclepro.2022.131264.

[46] ASHRAE Standard 14–2014, Measurement of Energy, Demand, and Water Savings, ASHRAE. Atlanta USA. 2014