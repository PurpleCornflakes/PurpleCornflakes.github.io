---
layout: post
title: Tensorflow -- Neural Style Transfer
category: Deep Learning
--- 

*When doing week4-course4 assignment1 of Andrew Ng's deep learning course, I struggled to understand what's going on. So I record pseudo-code here to help myself understand it better.*

The essence of *Neural Style Transfer* is to use a_C, a_S this two tensors and *a_G, the variable computed from G* to do forward propogation. **G**, the generated image, is exactly what we get from BP.

model = load_vgg_model("pretrained-model.mat")
This model is a dict containing all pre-trained tf.variables.

{% highlight Python linenos %}
def compute_content_cost(a_C, a_G):
    a_C -- tensor of dimension (1, n_H, n_W, n_C)
    a_G -- tensor of dimension (1, n_H, n_W, n_C)

    m, n_H, n_W, n_C = a_G.get_shape().as_list()
    
    a_C_unrolled = tf.transpose(tf.reshape(a_C, [-1, n_C]))
    a_G_unrolled = tf.transpose(tf.reshape(a_G, [-1, n_C]))

    J_content = (tf.reduce_sum(tf.square(tf.subtract(a_C_unrolled,a_G_unrolled))))/4/n_C/n_W/n_H
    # when compute J_style, we make use of tf.matmul()
    
    return J_content
{% endhighlight %}
We could test the above func below:

{% highlight Python linenos %}
tf.reset_default_graph()
with tf.Session() as test:
    tf.set_random_seed(1)
    a_C = tf.random_normal([1, 4, 4, 3], mean=1, stddev=4)
    a_G = tf.random_normal([1, 4, 4, 3], mean=1, stddev=4)
    J_content = compute_content_cost(a_C, a_G)
    print("J_content = " + str(J_content.eval()))
{% endhighlight %}

{% highlight Python linenos %}
def model_nn(sess, input_image, num_iterations = 200):

    sess.run(tf.global_variables_initializer())
    sess.run(model['input'].assign(input_image))
    
    for i in range(num_iterations):
        optimizer = tf.train.AdamOptimizer(2.0)
        train_step = optimizer.minimize(J)
        sess.run(train_step)
        
        generated_image = sess.run(model['input'])

        # Print every 20 iteration.
        if i%20 == 0:
            J= sess.run([J])
            print("Iteration " + str(i) + " :")
            print("total cost = " + str(Jt))
                
    # save last generated image
    save_image('output/generated_image.jpg', generated_image)
    
    return generated_image

if __name__ == "__main__":
    tf.reset_default_graph()
    # load image as np.array:
    content_image = scipy.misc.imread("image/louvre.jpg")
    content_image = reshape_and_normalize_image(content_image)
    # then similarly we get style_image

    # Assign the content image to be the input of the VGG model.  
    sess.run(model['input'].assign(content_image))
    # Select the output tensor of layer conv4_2
    out = model['conv4_2']
    # a_C now is a evaluated Tensor
    a_C = sess.run(out)
    # Set a_G to be the hidden layer activation from same layer. Here, a_G references model['conv4_2'] to be evaluated
    a_G = out
    # Compute cost
    J_content = compute_content_cost(a_C, a_G)
    # then similarly we get J_style

    J = alpha*J_content + beta*J_style

    sess = tf.Session()
    generated_image = sess.run(model['input'])
    model_nn(sess, generated_image)
{% endhighlight %}

###Note:
If we index an element by (i,j,k), then the corresponding axises are (0,1,2)
{% highlight Python linenos %}
a = np.array([[[1, 2, 3],
               [4, 5, 6]]])
a.reshape = (1,2,3)
{% endhighlight %}